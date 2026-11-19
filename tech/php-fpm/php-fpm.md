---
technology: "PHP-FPM / FastCGI"
kind: overview
audience: offensive-security
family: web-platforms
layer: "PHP application server: FastCGI protocol, process pools, per-pool identity"
language: C/PHP
license: "PHP License (php.net)"
homepage: "https://www.php.net/manual/install.fpm.php"
evidence: "12 writeups (command-context gate: php-fpm/fastcgi_pass/fastcgi_param SCRIPT_FILENAME/fastcgi_split_path_info/php-fpm.conf/pool.d paths/X-Powered-By: PHP-FPM/9000 tcp with php context; placeholder said 8 — gate adds 4 real files and kills 5 bare-port collisions: gRPC on 9000, authentik on 9000, Portainer on 9000, a wsgi socket, OCR noise)"
status: enriched
provenance: "corpus-mined from 12 writeups in the writeup corpus (see ../internal coverage ledger) + upstream-verified: CVE.org record CVE-2019-11043 (PHP Group CNA version ranges), php-fpm configuration references (pool.d www.conf, pm.status_path default-unset, ping.path, listen syntax), public CVE-2025-49132-chain PoC documentation of the pearcmd.php config-create technique, accessed 2026-11-12"
---

# PHP-FPM / FastCGI

> A binary protocol that turns "can I open a socket to 127.0.0.1:9000?" into "run this PHP file as that user". PHP-FPM trusts its FastCGI parameters completely — whoever can speak the protocol chooses the script, and `PHP_VALUE` can rewrite the interpreter's config per request.

## What it is

PHP-FPM (FastCGI Process Manager, part of PHP since 5.3.3) is the standard PHP application server behind nginx, Apache, and every containerized PHP stack. It manages pools of PHP workers defined in `/etc/php/<version>/fpm/pool.d/*.conf`: each pool sets a `listen` address (TCP `127.0.0.1:9000` or unix socket `/run/php/php<version>-fpm.sock`), a `user`/`group` the workers run as, and process-manager settings. The web server forwards each request over the FastCGI protocol — a length-prefixed binary framing carrying CGI-style parameters (`SCRIPT_FILENAME`, `PATH_INFO`, `QUERY_STRING`, and PHP-specific `PHP_VALUE`/`PHP_ADMIN_VALUE`).

The operator's mental model: **the web server vouches for nothing**. PHP-FPM performs no authentication on the socket and builds its entire execution decision from parameters the (assumed-trusted) peer supplies. `SCRIPT_FILENAME` names the file to execute — any path confusion that lets you change that parameter changes which PHP runs. This is why nginx misconfigs around `fastcgi_split_path_info` are RCEs, why an SSRF that reaches the FPM port is code execution, and why per-user pool sockets (observed: `php7.3-fpm-michael.sock`) are identity boundaries worth hunting.

The takeaway: FPM is invisible on the outside and unauthenticated on the inside. You attack it three ways — through the front proxy's forwarding rules (CVE-2019-11043), through any network position that reaches the listener directly (SSRF/pivot + FCGI client), or through configuration and source files once you can read the host (`pool.d/`, nginx `fastcgi_*` blocks, `pearcmd.php` on disk).

## The anatomy that matters on an engagement

    client → nginx/Apache (fastcgi_pass unix socket | 127.0.0.1:9000)
      → FastCGI BEGIN_REQUEST + PARAMS (SCRIPT_FILENAME, PATH_INFO, PHP_VALUE, ...)
      → pool worker (user = pool config, not web server user) executes SCRIPT_FILENAME
      misbehavior lives at:
        1. proxy→pool parameter confusion (fastcgi_split_path_info regexp → CVE-2019-11043)
        2. direct protocol access (SSRF/pivot → FCGI client chooses SCRIPT_FILENAME + PHP_VALUE)
        3. config-as-loot (nginx fastcgi_*, pool.d user=, per-user sockets)
        4. on-disk PHP toolbox (pearcmd.php turns any LFI into file-write RCE)

Three structural properties drive most findings:

1. **No auth on the listener.** If you can connect — SSRF, docker-network pivot, misbound `listen 0.0.0.0:9000` — you speak FastCGI as the trusted peer. Observed: internal pivot to port 9000, then a Python FCGI client executed arbitrary PHP as the pool user.
2. **`SCRIPT_FILENAME` is the contract.** The nginx stanza deciding it (`fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name` vs `$realpath_root…`, `fastcgi_split_path_info ^(.+\.php)(/.+)$`) is simultaneously the bug surface and the best recon loot on the box (observed: five engagements read it from nginx configs).
3. **Port 9000 tells you nothing.** The nmap `cslistener` service name is an IANA alias; observed collisions included a gRPC engine, an authentik HTTP API, and Portainer. Gate on process output (`php-fpm: master process`), `X-Powered-By: PHP-FPM`, or protocol behavior — never the port alone.

## Where you'll meet it

- **Every nginx+PHP vhost** — the default modern LEMP stack; the nginx config files are the first loot to read for `fastcgi_pass` targets and identity hints.
- **Containerized PHP apps** — fpm images bind 9000 inside the network namespace; SSRF or a same-network foothold speaks straight to it. Docker source builds also ship `/usr/local/lib/php/pearcmd.php` with `register_argc_argv` friendly defaults.
- **Multi-tenant hosts** — per-user pool sockets (`/run/php/<user>.sock`) separate tenants; finding the mapping in nginx config is finding whose code you can influence as whom.
- **SSRF surface in PHP apps** — a gopher-capable SSRF reaches FPM directly; FastCGI payload generators exist for exactly this.
- **Shared-hosting control panels** — LibreNMS-class apps with their own named socket (observed: `php-fpm-librenms.sock`).

## Reading the framework's mind

- `pm.status_path` is unset by default; when an admin enables it (`/status`), it answers to any client that sets `SCRIPT_NAME`/`SCRIPT_FILENAME` to the status path — including a direct `cgi-fcgi -bind -connect` with no web server involved. Pool name, process counts, last request URIs.
- CVE-2019-11043 (upstream, exploited in the field): nginx forwarding + `fastcgi_split_path_info` regexp anchored `^…$` → crafted `PATH_INFO` (newline `%0a`) causes a buffer underflow writing into FCGI data → RCE. Version gates: PHP 7.1.x < 7.1.33, 7.2.x < 7.2.24, 7.3.x < 7.3.11. The field also shows the negative gate: one engagement correctly ruled it out because the fronting server wasn't nginx+this config.
- A `502 Bad Gateway` is behavioral recon: it means nginx reached for a PHP-FPM worker that died or refused — the PHP app crashed on your input (observed once, confirming code execution side effects).
- `pearcmd.php` is a file-write gadget shipped with PHP PEAR: with `register_argc_argv=On`, the query string becomes argv, and `?+config-create+/<payload>+<outfile>` writes attacker-controlled PHP to disk. It converts any LFI-into-include on an FPM host into RCE (field-observed; Debian path `/usr/share/php/pearcmd.php`, source/Docker builds `/usr/local/lib/php/pearcmd.php`).
- Pool config `/etc/php/<v>/fpm/pool.d/www.conf` states the execution identity (`user=`, `listen=`) — reading it converts "PHP execution" into "code as user victor" precision for your report (field-observed).

## Where to go next

[[php-fpm-bug-bounty|PHP-FPM bug-bounty methodology]] — phased: fingerprint → config loot → version-gated CVE-2019-11043 → direct FCGI protocol attack → status/ping pages → pearcmd LFI-to-RCE → Xdebug remote-debug RCE → tool mapping.
