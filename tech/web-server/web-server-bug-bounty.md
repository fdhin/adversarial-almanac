---
technology: "Web servers (config-level attack surface across nginx/apache/caddy/IIS)"
kind: bug-bounty-methodology
audience: ai-agent
family: web
version: "1.0"
last-refresh: "2026-11-19"
status: upstream-only
provenance: "Methodology synthesized from the web-server vault overview + child pages ([[web-server]], [[web-server-nginx]], [[web-server-apache]], [[iis]]) and upstream-verified during drafting: nginx docs (merge_slashes, autoindex, stub_status, ETag), Apache httpd docs (mod_status/mod_info, FileETag, AllowOverride), Caddy admin-API docs, projectdiscovery/nuclei-templates (verbatim template filenames under http/misconfiguration/{apache,nginx} and http/exposures/configs, GitHub API 2026-11-19), nmap http-enum NSEDoc, slowhttptest(1) man page, CVE-2003-1418 (ETag inode disclosure). Config-finding classes are MISCONFIGURATION classes, not CVEs, except where a CVE is named."
source: "upstream docs + nuclei-templates repo listing + tool man pages, fetched/verified 2026-11-19"
---

Companion pages: [[web-server|Web servers router]] (daemon dispatch, the config-pull pattern), [[web-server-nginx|nginx attack surface]], [[web-server-apache|Apache httpd attack surface]], [[iis|IIS attack surface]]. TLS findings cross-link [[ssl-tls]]; smuggling/desync across a proxy tier is [[http-proxy]]; exposed VCS metadata is [[git-exposed]]. Tool pages live in the separate tools vault; this document names tools in prose only.

---

# Web-server config attack surface — a bug hunter's field guide

**In scope:** the HTTP-daemon configuration tier across the four servers you actually meet — **nginx**, **Apache httpd**, **Caddy**, and **IIS** — treated as a cross-product of one methodology against four config dialects. The product is the *config-level* bug class that lives below the application: fingerprinting the server/version/module set, config- and backup-file exposure, default-vhost serving, path-handling quirks, status/module endpoint exposure, TLS-termination posture, and slow-HTTP DoS posture.

**Out of scope:** application logic above the daemon, and the application frameworks that have their own pages ([[fastapi]], [[flask]], [[django]], [[node-express]], [[tomcat]], [[php-fpm]]). When a finding is really the framework's, pivot to that page.

**Audience & discipline:** authorized-program operators. Every probe below is single-request, low-volume, evidence-first. The two genuinely dangerous classes here — slow-HTTP (Phase 6) and any traversal that reads system files — are demonstrated with ONE request and a redacted proof, never a flood or a bulk dump. The offensive craft is the product; the discipline is what keeps it deployable.

## The web-server security model in one page

A web server's job is to map a URL to a file or an upstream, and to enforce the first layer of access control while doing it. Every config-tier bug is one of those two jobs done wrong:

- **The mapping builds a path the admin did not intend.** `alias`/`root` splicing, `Alias`/`DocumentRoot` mistakes, IIS per-directory `web.config` overrides, Caddy `file_server` roots pointed too high. Result: source, backups, or `/etc/passwd` served as "static content."
- **Normalization disagrees between tiers.** What nginx/Caddy/IIS collapses, decodes, or merges is not what the upstream (or the next proxy) does. Every disagreement is a potential access-control bypass — and it is a *config* property (which normalization flags are set), not a version.
- **A protection depends on the daemon honoring it.** `.htaccess` deny rules vanish behind a non-Apache front; Basic-auth on an internal vhost evaporates if the catch-all vhost serves the same root; `server_tokens off` hides a banner but not an ETag. Protections are daemon-relative — never assume the one you can't see is running.
- **Config, backup, and status files are administrator-authored.** They hold the next step of the chain: internal hostnames, upstream maps, credential-file paths, machine keys. The config tier leaks *operator* secrets, which are worth more than any single app bug.

The single most productive sentence: **fingerprint the daemon first, because no bug class transfers between them and no protection is guaranteed to be running.** A `.htaccess` rule, a `merge_slashes`, a `web.config` handler map — each exists in exactly one dialect, and the money is in the gap between what the admin wrote and what the daemon in front actually enforces.

### Where the money is (triage priority)

| Priority | Class | Where it lives (dialect) | Typical severity |
|---|---|---|---|
| 1 | Config/backup file exposure → secrets | any: `.bak`/`.orig`/`~`/`.save` of nginx.conf, web.config, .env; `.htpasswd`; `machineKey` | High–Critical |
| 2 | Path-handling bypass / alias traversal | nginx off-by-slash `alias`; `merge_slashes off` seam; IIS `web.config` remap; encoded-traversal pass-through | High–Critical |
| 3 | Directory listing / autoindex on | nginx `autoindex on`; Apache `Options +Indexes` / mod_negotiation; IIS directory browsing | Medium–High |
| 4 | Default/catch-all vhost serving internal site | nginx `default_server`; Apache first-vhost; missing SNI match | Medium–High |
| 5 | WebDAV write enabled | Apache mod_dav; IIS WebDAV; PUT/MOVE to a servable dir → upload-to-RCE | High–Critical |
| 6 | Status/info module exposure | Apache `/server-status`,`/server-info`; nginx `stub_status`; vhost-traffic-status | Low–Medium (recon amplifier) |
| 7 | TLS-termination misconfig | plaintext behind terminator; weak suites; redirect-to-http — see [[ssl-tls]] | Low–High (context) |
| 8 | Slow-HTTP protection absent | no `limit_req`/mod_reqtimeout/connectionTimeout; Slowloris posture | REPORT-class (Low–Med) |
| 9 | Version/module fingerprint disclosure | `Server` banner, `X-Powered-By`, `X-AspNet-Version`, ETag inode, default error page | Info (chains into 1–2) |

Phase 0 builds the target map; Phases 1–3 are where payouts concentrate; Phases 4–6 round out coverage and posture reporting.

---

## Phase 0 — Fingerprint: server, version, modules

Goal: name the daemon, pin a version band, and enumerate the enabled module/handler set — because everything after this is dialect-specific. Trust no single signal; corroborate 2–3.

### 0.1 The banner and its friends

```bash
curl -sI https://example.com/ | grep -iE \
  'server|x-powered-by|x-aspnet|x-aspnetmvc|via|x-cache|etag|last-modified'
```

| Signal | nginx | Apache httpd | Caddy | IIS |
|---|---|---|---|---|
| `Server` | `nginx` / `nginx/1.27.1` (hidden by `server_tokens off`) | `Apache/2.4.58 (Ubuntu)` — distro tag is a free OS hint | `Caddy` — **never a version** | `Microsoft-IIS/10.0` — version = OS generation (10.0 = 2016+, 8.5 = 2012 R2, 7.5 = 2008 R2) |
| `X-Powered-By` | (app sets it) | (app sets it) | — | `ASP.NET` (+ `X-AspNet-Version`, `X-AspNetMvc-Version`) |
| Default error page | "404 Not Found / nginx" (centered, names nothing else) | "Not Found ... Apache/2.4.x Server at ... Port 80" — **echoes version+host** when `ServerSignature On` | plain `404` text, no chrome | yellow/blue ASP.NET YSOD, or `HTTP Error 404.0 - Not Found` detailed page |
| Default landing page | "Welcome to nginx!" | "Apache2 Ubuntu Default Page" / CentOS test page | blank 200 | "IIS Windows Server" splash (`iisstart.htm`) |

**The banner lies in both directions.** A rewritten `Server` header does not update the default page text (an nginx welcome page naming `/etc/nginx/nginx.conf` is an nginx box regardless of banner), and a reverse-proxy front may stamp its own `Server` over a different back end — watch for a `Via`/`X-Cache`/doubled header set that betrays two tiers.

### 0.2 ETag format — a passive version/daemon marker

ETags are generated differently per daemon and leak build posture without a banner:

```bash
curl -sI https://example.com/robots.txt | grep -i etag
# Apache <2.4 default:  ETag: "1a2b3c-4d5-5f0a1b2c3d4e5"   <- THREE hex fields = inode-size-mtime
# Apache >=2.4 / nginx: ETag: "68f0a1b2-4d5"               <- TWO fields = (mtime/size), no inode
```

A three-field hex ETag is Apache with `FileETag` still including **INode** — that is CVE-2003-1418 (inode disclosure; aids NFS file-handle guessing), and it also pins the server to pre-2.4-default behavior. nginx's ETag is `"<hex-last-modified>-<hex-content-length>"`; IIS uses a different opaque form again. Weak-ETag (`W/"..."`) vs strong is a further tell. Record the exact string — it is Info on its own, a version anchor for Phases 1–2.

### 0.3 Automated fingerprint, then confirm by hand

```bash
whatweb -a3 https://example.com/            # -> HTTPServer[Apache/2.4.58], nginx[1.27.1], X-Powered-By strings
nmap -sV -p80,443 --script=http-enum,http-headers,http-methods 198.51.100.10
```

`whatweb` reads the `Server`/`X-Powered-By` headers and asset fingerprints and prints `nginx[1.27.1]`-style version tags in its summary (verbose `-v` shows which pattern matched). `nmap --script=http-enum` walks a built-in path list and prints discovered resources with labels (`/admin/ Possible admin folder`); it is categorized *intrusive/vuln*, so run it only in scope. It bails with "ambiguous response" when the server 200s on everything (a catch-all — a Phase 2 signal in itself).

**Module/handler enumeration** is daemon-specific and mostly inferred from behavior:
- **Apache** — probe `/server-status`, `/server-info` (mod_status/mod_info, Phase 4); a `.htaccess`-honoring directory proves `AllowOverride`; `.php` execution proves mod_php/php-fpm handler.
- **nginx** — `/basic_status`-style endpoints (stub_status, Phase 4); `.htaccess` is **ignored** (vendor protections written for it are dead — test the "protected" dir directly).
- **IIS** — extension→handler map; `/aspnet_client/` existing proves ASP.NET is wired; `.config`/`.asp`/`.ashx` acceptance is the Phase 1/5 upload surface.
- **Caddy** — minimal modules; the interesting one is the admin API (Phase 4).

**Decision point:** once the daemon is named, jump to its dialect rows below. If two daemons answer (proxy + back end), map BOTH — the normalization seam between them is Phase 3's richest target.

---

## Phase 1 — Config- and backup-file exposure

Goal: pull the administrator-authored files that hold the next step. This is the single most repeated config-tier win, and it needs no CVE — only a servable path.

### 1.1 The backup/temp-file sweep

Editors and admins leave shadows of the config inside the webroot. Request each candidate and diff against the 404 baseline:

```bash
base=https://example.com
for f in .env .env.bak web.config web.config.bak web.config.old Web.config.txt \
         nginx.conf nginx.conf.bak nginx.conf.save .htaccess .htaccess.bak \
         httpd.conf app.config appsettings.json wp-config.php.bak config.php~ \
         .DS_Store '%2e%2e' 'index.php.swp'; do
  code=$(curl -s -o /dev/null -w '%{http_code} %{size_download}' "$base/$f")
  echo "$code  $f"
done
# -> 200 4821  web.config.old   <- served as text/plain: connectionStrings + machineKey
```

Extension/suffix matrix to permute against any known filename: `.bak .old .orig .save .swp .swo ~ .1 .txt .inc .dist .sample .copy` plus editor temp forms (`#file#`, `.file.un~`). The killer property: **a backup extension strips the handler** — `web.config` executes as config but `web.config.old` is served as static text, handing you `<connectionStrings>` and `<machineKey>` (IIS ViewState-forging → RCE; see [[iis]]). Same for `index.php.bak` (PHP source, not execution) and `.env.bak` (secrets).

### 1.2 What each dialect's config buys you

| File (per dialect) | Pull priority | Yields |
|---|---|---|
| nginx `sites-enabled/*`, `conf.d/*.conf` | 1 | `server_name` (hidden vhosts), `root`/`alias`, `proxy_pass` (upstream map), `auth_basic_user_file` |
| nginx `.htpasswd` (often in `conf.d/`) | 2 | `$apr1$`/bcrypt Basic-auth hashes → john/hashcat offline |
| Apache `sites-{enabled,available}/*`, `.htaccess` | 1 | `DocumentRoot`, `Alias`, `AuthUserFile`, `Require`/`Deny` logic, `RewriteRule` |
| Apache `.htpasswd` (`AuthUserFile` path) | 2 | same hash store |
| IIS `web.config` (and `applicationHost.config` if reachable) | 1 | `connectionStrings`, `<machineKey>`, handler maps, rewrite rules |
| Caddy `/etc/caddy/Caddyfile` | 1 | routes, `reverse_proxy` upstreams, TLS identity |
| any default-vhost landing page | 3 | names its own config path (honest daemon witness) |

With any file-read primitive elsewhere (LFI, XXE `file://`, SQLi `load_file()`, an SSRF that speaks `file://`, or an exposed `.git`/backup dir — see [[git-exposed]]), point it at the daemon config *before* fishing `/etc/passwd`: vhosts enumerate internal hostnames to add to your `/etc/hosts` and fuzz, `proxy_pass`/`reverse_proxy` lines map the interior, and `AuthUserFile` paths are the next read. The overview ([[web-server]]) documents the full config-pull pattern; this phase is its black-box front door.

### 1.3 Templated detection

```bash
nuclei -u https://example.com -t http/exposures/configs/ -t http/misconfiguration/
# verified template names (nuclei-templates, 2026-11-19):
#   http/exposures/configs/nginx-config.yaml, apache-config.yaml, httpd-config.yaml, web-config.yaml
#   http/misconfiguration/apache/apache-mod-negotiation-listing.yaml   (dir listing via content-negotiation)
#   http/misconfiguration/apache/apache-filename-enum.yaml             (mod_negotiation name leak)
```

Run by directory, not a blind "all templates" sweep — `http/exposures/configs/` and `http/misconfiguration/` are the two that matter here, and `-tags config,exposure` narrows further. Treat every hit as a lead to verify and capture by hand, never as the report itself.

### 1.4 Directory listing (autoindex)

A listing turns a backup directory into a self-service index:

```bash
curl -s https://example.com/uploads/ | grep -iE 'Index of|autoindex|<title>Directory'
# Apache -> "<title>Index of /uploads</title>" + mod_autoindex column headers (Name/Last modified/Size)
# nginx  -> "<title>Index of /uploads/</title>" + <pre> listing   (autoindex on)
# IIS    -> "<pre>" listing with [To Parent Directory] link       (directory browsing enabled)
```

Dialect cause: nginx `autoindex on;`, Apache `Options +Indexes` (or mod_negotiation `MultiViews` leaking sibling names via `apache-filename-enum`), IIS `<directoryBrowse enabled="true">`. Impact is whatever sits in the listed dir — `.git/`, DB dumps, `.swp` files, artifact tarballs. Pair a listing with 1.1's suffix matrix: the listing *names* the backup you then fetch.

---

## Phase 2 — Default / catch-all vhost serving

Goal: reach the site that answers when the `Host` doesn't match a configured vhost — usually an internal default left enabled beside the real site.

A server with no SNI/`Host` match falls back to a default vhost: nginx's `listen ... default_server` block (or the first `server{}`), Apache's first-loaded `<VirtualHost>`, IIS's site with no host-header binding. That fallback frequently serves staging, an admin panel, or the raw `DocumentRoot` the public hostname hides.

```bash
ip=198.51.100.10
curl -s -o /dev/null -w '%{http_code} %{size_download}\n' https://$ip/ -k            # IP, no Host
curl -s -o /dev/null -w '%{http_code} %{size_download}\n' https://$ip/ -k -H 'Host: nonexistent.invalid'
curl -s https://$ip/ -k -H 'Host: localhost' | head          # some defaults bind to localhost only
```

**Signals:** a different body/length/redirect for the bare-IP or bogus-`Host` request than for `Host: example.com` means a distinct catch-all is reachable. Follow with vhost brute forcing (`ffuf -H 'Host: FUZZ.example.com'` against an internal-name wordlist seeded from Phase 1's looted `server_name` list) and diff each response. The classic chain: a distribution-default vhost (`sites-enabled/default`) left enabled, serving `/var/www/html` with an un-audited app the real site never linked. This is also where a TLS cert's SAN list (Phase 5 / [[ssl-tls]]) hands you hostnames to replay as `Host`.

**Decision point:** if the catch-all serves a different app, treat it as a fresh target and restart Phase 1 against it — default vhosts are the least-audited code on the box.

---

## Phase 3 — Path-handling quirks (config class, not CVE)

Goal: exploit the gap between how the front daemon maps/normalizes a path and how the filesystem or upstream interprets it. These are **configuration/parser classes with no CVE** (state that in the report to prevent miscitation) — except the two named Apache CVEs.

### 3.1 nginx off-by-slash alias traversal

The signature nginx misconfiguration (Orange Tsai parser-logic research; **no CVE**):

```
location /static {          # location WITHOUT trailing slash
    alias /opt/app/static/; # alias WITH trailing slash
}
```

Everything after the literal prefix `/static` is spliced onto the alias, including `..`:

```bash
# Non-destructive detection FIRST — prove the boundary isn't enforced before traversing:
curl -s -o /dev/null -w '%{http_code}\n' https://example.com/staticfoo   # same resource as
curl -s -o /dev/null -w '%{http_code}\n' https://example.com/static/foo  # /static/foo  => splice confirmed
# Then the minimal proof (one file, redacted):
curl -s --path-as-is https://example.com/static../etc/passwd | head -1
# filesystem path becomes /opt/app/static/../etc/passwd
```

A 301 from `/static..` to `/static../` is nginx's directory-normalization courtesy confirming the escaped dir exists. Impact is whatever neighbors the alias — app source, sibling vhost roots, the app's own config one dir up. The same slash bug lives in `proxy_pass` (`/api../` → the upstream root, exposing hidden admin/status handlers). The `gixy` config scanner ships an `alias_traversal` check for this exact shape — run it over any looted nginx config from Phase 1.

### 3.2 Normalization seams: merge_slashes, %2f, dot-segments

`merge_slashes` is **on by default** in nginx (`//admin` ≡ `/admin`). An admin who turns it OFF (to support legitimate `//` paths) creates a front/back mismatch: a `location /admin` rule won't match `//admin`, but the upstream may re-normalize it — ACL bypass by double slash. `%2f` is **not** decoded by nginx by default, so `/%2fadmin` and `/admin` differ to the front and may converge at the back. Probe every denied path through BOTH tiers' lens:

```bash
for p in '/admin' '//admin' '/./admin' '/%2fadmin' '/admin/..;/admin' '/admin%2f..%2f'; do
  printf '%s -> ' "$p"; curl -s -o /dev/null -w '%{http_code}\n' --path-as-is "https://example.com$p"
done
```

A denied path (403/401) that turns 200 under one encoding is an auth-bypass. Encoded-traversal **pass-through** is the cross-daemon version: Caddy's `reverse_proxy` forwards `..%2f` to the upstream unnormalized by default (documented behavior, **no CVE**) — if a Caddy/nginx-fronted app reflects or redirects a path, rewrite the target to `/export/..%2f..%2f..%2fetc/passwd`. IIS has its own `;`/`::$DATA`/trailing-dot quirks (see [[iis]]). When a front guards a back end, smuggling/desync across that boundary is [[http-proxy]] territory.

### 3.3 The two Apache traversal CVEs (banner+config gated)

| CVE | Version | Condition | Effect |
|---|---|---|---|
| CVE-2021-41773 | 2.4.49 | non-default `<Directory>` (`Require all granted` or missing) + encoded `.%2e/` | file read outside docroot; **RCE if mod_cgi enabled** |
| CVE-2021-42013 | 2.4.50 | same; 41773 fix bypassed by double-encoding `.%%32%65/` | same; fixed in **2.4.51** |

The banner alone is insufficient — a 2.4.49/50 box with the default `Require all denied` is NOT vulnerable. Confirm the config condition (from a looted `<Directory>` block in Phase 1) before reporting. One-request proof: `curl --path-as-is 'https://example.com/cgi-bin/.%2e/.%2e/etc/passwd'`.

---

## Phase 4 — Module & status endpoint exposure, WebDAV

Goal: the capability endpoints that leak interior state or accept writes.

### 4.1 Status / info modules

```bash
for u in /server-status /server-status?auto /server-info /status /stub_status \
         /basic_status /nginx_status /nginx-status; do
  echo "$(curl -s -o /dev/null -w '%{http_code}' https://example.com$u) $u"
done
```

- **Apache `mod_status`** — `/server-status` lists active requests with **real client IPs, vhost names, and full request lines** (`?auto` is the machine-readable form); `mod_info` `/server-info` dumps the entire loaded config including module list and directives. Both are recon goldmines when not IP-restricted. nuclei: `apache-server-status.yaml`, `apache-server-status-localhost.yaml` (the localhost variant catches servers that allow `127.0.0.1` but are reachable via an SSRF/`X-Forwarded` trick).
- **nginx `stub_status`** — connection/request counters only (less sensitive, still an unintended exposure). nuclei: `nginx-status.yaml`; the vhost-traffic-status module exposes per-vhost byte/response-code tables (`nginx-vhost-traffic-status.yaml`), and `nginx-status-403-bypass.yaml` targets the common "allow localhost" ACL defeated by a spoofed source.
- **Caddy admin API** — listens on `localhost:2019` by default and serves the full running config as JSON (`GET /config/`) to anything that reaches it locally — a prime SSRF target (`http://localhost:2019/config/` returns routes + upstreams).

Severity is usually Low–Medium on its own, but `/server-status`'s client-IP and request-line leak is a High-adjacent recon amplifier, and `/server-info`'s config dump collapses Phase 1 for you.

### 4.2 WebDAV enabled semantics

WebDAV turns the webroot into a writable filesystem — and a writable servable directory is upload-to-RCE:

```bash
curl -s -X OPTIONS https://example.com/ -i | grep -iE '^allow|^dav|^ms-author-via'
# Allow: ... PUT, DELETE, MOVE, COPY, PROPFIND ...   and   DAV: 1,2   => WebDAV live
curl -s -X PROPFIND https://example.com/ -H 'Depth: 1' --data '' -i | head   # 207 Multi-Status = dir listing via DAV
```

- **Apache `mod_dav`/`mod_dav_fs`** — a `<Location>` with `Dav On` and loose auth lets `PUT /shell.XXX`; if the dir also runs a handler (mod_php), `PUT shell.phtml` → execution.
- **IIS WebDAV** — `PUT`/`MOVE` chains (the classic `PUT file.txt` then `MOVE` to `file.aspx`) bypass upload filters; the `nuclei` Apache WebDAV PROPFIND template flags arbitrary directory listing.
- **The test discipline:** `OPTIONS`+`PROPFIND` are read-only and safe; `PUT` of a single harmless marker file (then `DELETE` it, or report the `PUT` 201 without leaving a shell) is the proof. Never leave an executable artifact — upload a `.txt`, show the 201 + readback, clean up.

---

## Phase 5 — TLS-termination posture

Goal: the boundary where the daemon speaks (or fails to speak) TLS. Full suite/cert analysis is [[ssl-tls]]; here are the *config-tier* tells.

- **Plaintext behind a terminator.** If the daemon serves `http://` on its own port while TLS terminates upstream (a load balancer, a sidecar), application data crosses the internal segment in the clear — a deployment-boundary property no header asserts. On an authorized assessment, capture on a segment you may observe (tcpdump/mitmproxy) and confirm whether the daemon's port is TLS or plaintext. In most bounty programs this is a hardening note; name it as a requirement on the operator, not a property of the app.
- **Redirect and HSTS gaps.** `curl -sI http://example.com/` — a `200` instead of a `301` to `https://`, or a `Location:` built from a client `Host`/`X-Forwarded-Host` (open-redirect / cache-poison seed), or a missing `Strict-Transport-Security`, are config-tier TLS findings.
- **Terminator cert vs back-end identity.** The front cert's SAN list enumerates vhosts to replay in Phase 2. A back end that trusts `X-Forwarded-Proto` blindly can be told it's "already HTTPS" and skip its own redirect.
- **Weak config surface** (full depth in [[ssl-tls]]): `ssl_protocols`/`SSLProtocol` allowing TLS1.0/1.1, `ssl_ciphers` with export/NULL/RC4, missing OCSP stapling. `nmap --script ssl-enum-ciphers -p443 198.51.100.10` and `testssl.sh` are the tools; cite findings to the [[ssl-tls]] methodology for severity.

---

## Phase 6 — Slow-HTTP / resource-limit posture (REPORT-class)

Goal: assert, with a single bounded probe, whether the daemon has connection/rate/timeout limits — a DoS *posture* finding, reported as a config gap, never demonstrated as an outage.

Slow-HTTP attacks (Slowloris = slow headers, slow POST body, slow read) hold connections open with a trickle of bytes, exhausting the worker/connection pool. The defenses are config directives: Apache `mod_reqtimeout` (`RequestReadTimeout`), nginx `client_header_timeout`/`client_body_timeout` + `limit_conn`/`limit_req`, IIS `connectionTimeout`/`headerWaitTimeout`, Caddy's built-in timeouts. Their **absence** is the finding.

```bash
# Single bounded check — report the POSTURE, do not sustain an outage. Authorized + low connection count.
slowhttptest -H -c 50 -i 10 -r 20 -t GET -g -o slowpost_stats \
  -u https://example.com/ -x 24 -p 3
#  -H slow-headers (Slowloris) mode | -B slow body | -R slow read
#  -c connections (keep SMALL) | -g -o emit CSV/HTML for the report | -p seconds to declare "service unavailable"
```

Read the generated stats/HTML: if a modest connection count moves the server from "available" toward "pending/closed" within the probe window, the timeout/limit directives are absent — report the *configuration gap* (no `mod_reqtimeout` / no `limit_conn`) with the stats graph as evidence, and **stop the test immediately**. Frame impact as "a single low-bandwidth client can degrade availability because no slow-request timeout is configured," with the remediation directive named per dialect. On a bounty program with no DoS clause, this is a config observation, not a live demonstration — capping `-c` low and `-l` short keeps it inside the rules.

---

## Reporting & evidence standards

The report is the product. Structure every finding identically:

1. **Title — impact first, dialect named.** "Unauthenticated read of `web.config` (connectionStrings + machineKey) via `web.config.old` backup on IIS 10" beats "info disclosure."
2. **Fingerprint basis.** The corroborated daemon+version evidence (banner + ETag + default page), so the triager trusts the dialect-specific claim.
3. **PoC — one request per step**, copy-pasteable, `--path-as-is` where normalization matters, expected-vs-actual. Use `example.com` / `*.example.com` / `198.51.100.0/24` only.
4. **Evidence.** Redacted response bodies/headers; for a traversal, ONE sensitive file truncated to the proof (first line of `/etc/passwd`), never a dump. For slow-HTTP, the stats graph, not a sustained outage. Delete any marker file you `PUT`.
5. **Remediation — dialect-specific directive.** `AllowOverride None` + `FileETag MTime Size`; `merge_slashes on` + trailing-slash parity on `location`/`alias`; `<directoryBrowse enabled="false">`; `RequestReadTimeout header=20-40`. Naming the exact directive signals root-cause understanding.

**Severity rules of thumb:** config/backup file with secrets (machineKey, connectionStrings, `.htpasswd`) — High–Critical; traversal/normalization read of system files — High; WebDAV write → RCE — Critical; directory listing of sensitive dir — Medium–High; default-vhost internal app — Medium–High; status-module client-IP/request leak — Low–Medium; slow-HTTP posture, ETag inode, banner disclosure — Low/Info. Always map to the program's rubric.

**Falsifiable closeout** — frame coverage as claims you tried to *disprove*: "no backup/temp shadow of any config is servable" (rejected if the 1.1 sweep stays 404), "front and back normalize the denied path identically" (rejected if any 3.2 encoding bypasses), "every protection I can't see is actually enforced" (rejected if a `.htaccess`/Basic-auth dir serves directly). Record the method, the payloads, and the boundary you did not cross.

---

## CTF notes

Config-misconfig boxes are a staple — the intended path is almost always a config-tier chain, not a memory-corruption exploit:

- **Fingerprint cheaply, then think in the dialect.** The default error page, ETag field count, and `X-Powered-By` tell you nginx vs Apache vs IIS before you fuzz — and the box's bug class follows from that (alias traversal on nginx, `.htaccess`/mod_cgi on Apache, `web.config`/shortname/ViewState on IIS — see [[iis]]).
- **The backup extension is the intended shortcut.** `index.php.bak`, `config.php~`, `.env.save`, `web.config.old` served as text is the single most common "foothold is in plain sight" trick. Run the 1.1 matrix first thing.
- **vHost + `/etc/hosts` is a required reflex.** Bare-IP or a hint in a cert SAN / page comment points at a vhost you must add to `/etc/hosts` to reach the real app (Phase 2). Boxes routinely gate the whole challenge behind a non-default `Host`.
- **alias/autoindex → source → secret.** An `autoindex on` upload dir or an off-by-slash alias leaks source, whose hardcoded creds unlock the next tier. Chain Phase 1.4 → 3.1 → app.
- **Status endpoints are free recon.** `/server-status` on a CTF Apache hands you other players' request lines and internal vhost names; `/server-info` dumps the config you were meant to enumerate.
- **WebDAV `PUT` is a frequent intended RCE.** `OPTIONS` showing `PUT`/`MOVE` on a servable dir is the whole foothold on many Windows/IIS and legacy-Apache boxes.

---

## References

- nginx docs — `ngx_http_core_module` (`merge_slashes`, `alias`, `root`, `autoindex`), `ngx_http_stub_status_module`, ETag/`etag` — https://nginx.org/en/docs/
- gixy (nginx config scanner, `alias_traversal`, `status-page-exposed` checks) — https://github.com/dvershinin/gixy and https://gixy.getpagespeed.com/checks/status-page-exposed
- Apache httpd docs — `mod_status`, `mod_info`, `core` (`FileETag`, `AllowOverride`, `Options`), `mod_reqtimeout`, `mod_dav` — https://httpd.apache.org/docs/current/
- Apache CVE-2021-41773 / CVE-2021-42013 advisory (2.4.49/2.4.50 path traversal; fixed 2.4.51) — https://httpd.apache.org/security/vulnerabilities_24.html
- CVE-2003-1418 — Apache ETag inode information disclosure — https://nvd.nist.gov/vuln/detail/CVE-2003-1418
- Caddy admin API (localhost:2019, `/config/`) — https://caddyserver.com/docs/api
- projectdiscovery/nuclei-templates — `http/exposures/configs/{nginx-config,apache-config,httpd-config,web-config}.yaml`, `http/misconfiguration/apache/{apache-server-status,apache-server-status-localhost,apache-mod-negotiation-listing,apache-filename-enum}.yaml`, `http/misconfiguration/nginx/{nginx-status,nginx-vhost-traffic-status,nginx-status-403-bypass,nginx-api-traversal}.yaml` (verified via GitHub API, 2026-11-19) — https://github.com/projectdiscovery/nuclei-templates
- nmap `http-enum` NSEDoc — https://nmap.org/nsedoc/scripts/http-enum.html
- WhatWeb — https://github.com/urbanadventurer/WhatWeb
- slowhttptest(1) man page (`-H`/`-B`/`-R` modes, `-c`/`-g`/`-o`/`-p`) — https://github.com/shekyan/slowhttptest/wiki/InstallationAndUsage
- OWASP WSTG — Fingerprint Web Server; Review Webserver Metafiles — https://owasp.org/www-project-web-security-testing-guide/
- Companion vault pages: [[web-server]], [[web-server-nginx]], [[web-server-apache]], [[iis]], [[ssl-tls]], [[http-proxy]], [[git-exposed]]

*Drafted 2026-11-19 from the web-server vault overview + child pages and upstream sources verified at draft time (nginx/Apache/Caddy docs, nuclei-templates repo listing via GitHub API, nmap NSEDoc, slowhttptest man page, CVE-2003-1418 and the 2021 Apache traversal pair). Config-finding classes are misconfiguration classes with no CVE except where named. Model claude-opus-4-8.*
