---
technology: "Apache HTTP Server (httpd)"
kind: overview
audience: offensive-security
family: web
layer: HTTP server (TCP/80, 443; config /etc/apache2 or /etc/httpd)
language: C
first-released: 1995
license: "Apache-2.0"
homepage: "https://httpd.apache.org"
github-repo: "apache/httpd"
provenance: "evidence-grounded (10 writeups gated at command-context: .htaccess upload/abuse incl. mod_headers expression file read, .htaccess read→bypass, AuthUserFile disclosure→crack, webroot inference; see ../internal coverage ledger — plus 16 config-disclosure-only machines on the router page) + upstream-verified: httpd docs (AllowOverride, mod_headers Header expr=, ap_expr functions incl. `file`/`base64`), httpd security advisories for CVE-2021-41773/CVE-2021-42013 (2.4.49/2.4.50, fixed 2.4.51), SeedDMS advisory CVE-2019-12744 (context for the .htaccess-vs-nginx mismatch). Pass 2 wave C, 2026-11-12."
---

# Apache HTTP Server (httpd)

> The server that executes its configuration out of the document root: write one `.htaccess` file into any servable directory and you have changed server behavior — the write primitive a thousand upload forms hand you becomes arbitrary file read, PHP execution, or an auth bypass.

## What it is

Apache httpd (1995, Apache-2.0) is the process-based HTTP server that still dominates classic LAMP hosting. Two design decisions define its attack surface for an operator:

1. **Per-directory configuration.** httpd re-reads `.htaccess` files from every servable directory **on every request**, when `AllowOverride` permits it. `.htaccess` is not application code — it is server configuration that happens to live where your uploads land. Any file-write into a servable directory is therefore a server-config write.
2. **Everything is a module.** `mod_php`, `mod_headers`, `mod_rewrite`, `mod_cgi`, `mod_status` — capabilities are enabled per-directory and per-vhost, and the enabled set (`/etc/apache2/mods-enabled/`) is usually looser than anyone remembers.

The operator takeaway: on Apache, hunt the **write** primitive with the same energy you usually spend on reads — and when you have a read, the vhost configs under `sites-enabled/` are the target list (see the [[web-server|router page]] for the config-disclosure pattern; this page owns the Apache-specific surface).

## The anatomy that matters on an engagement

    request → vhost (sites-enabled/*: server_name, DocumentRoot)
           → <Directory> blocks + per-directory .htaccess (AllowOverride gates what it may contain)
           → modules: mod_php (handler for .php), mod_rewrite (URI rewriting),
                      mod_headers (response header templating), mod_cgi (binary execution),
                      mod_status (/server-status)
           → filesystem: DocumentRoot + URI, Alias-mapped dirs, .htpasswd sidecars

## `.htaccess` as the write target (the crown jewel)

When an upload, an archive extract, a webshell-limited write, or an authenticated CMS "theme/asset" path lets you place a file in a directory httpd serves, do not reach for a PHP webshell first — reach for `.htaccess`. One file reconfigures the server for that directory:

- **Execute your non-PHP extension.** `.htaccess` can make httpd run uploaded images as PHP:

  ```
  AddType application/x-httpd-php .png      # the .png you uploaded is now executed
  ```

  Real chains: an upload filter that blocked `.php` defeated by a `.htaccess` instructing the server to treat image extensions as PHP; another shipped `.htaccess` inside an uploaded theme archive so the payload directory stayed web-executable regardless of extension checks. On servers where only `.phar/.php/.phtml` execute by policy, `.htaccess` rewrites that policy.

- **Arbitrary file read via mod_headers expressions — no code execution at all.** `mod_headers` lets `Header` directives embed ap_expr expressions, and the expression language ships file-reading and encoding functions (documented in httpd's expression docs: `file` = "read contents from a file", `base64` = encoder; both reachable in this context on stock distro builds). Append one line to a writable `.htaccess` and read the file back out of the response headers:

  ```
  Header set X-Leak "expr=%{base64:%{file:/etc/passwd}}"
  ```

  Request anything in that directory, take `X-Leak` from the response, base64-decode. Loop it over `/etc/shadow`, app configs, and keys. This has produced full arbitrary-file-read from a single uploadable `.htaccess` on a stock `Apache/2.4.x (Ubuntu)`. It needs no code-exec module and no exotic directive — verify with the harmless documented example first (`expr=%{md5:foo}`) to confirm expressions evaluate before escalating.

- **`php_value` injection (mod_php).** Where PHP runs as an Apache module, `.htaccess` can set PHP INI entries per-directory: `php_value auto_prepend_file /etc/passwd`-style prepends drag arbitrary files into every request (file read), `php_value` engine toggles break the app, and filter chains (`php://`) compose with it.

- **Behavior surgery with `IfModule`.** Writing `<IfModule mod_rewrite.c>RewriteEngine Off</IfModule>` into an application's `.htaccess` disables its rewrite layer — used in the wild to break a CMS's routing protections (e.g., expose direct file access a rewrite rule was denying).

- **The reverse direction: read `.htaccess` for the rules.** A file read reaching a servable directory should pull `.htaccess` before anything else — it contains the app's access-control logic (header gates, `Deny`/`Allow`, `Require` lines, redirect rules). A real chain read `SetEnvIfNoCase Special-Dev "only4dev" Required-Header / Deny from all / Allow from env=Required-Header` and bypassed the whole gate by sending the one header the rule demanded.

**Precondition check**: `.htaccess` only executes when `AllowOverride` permits the directive types (defaults are conservative on modern distros but hosting panels and CMS documentation push `AllowOverride All`). Test cheaply: upload a `.htaccess` setting a junk response header (`Header set X-Test yes`) — if the header appears, overrides live.

## .htpasswd / Basic-auth disclosure

Apache Basic auth hashes live in `.htpasswd` files referenced by `AuthUserFile` — both in vhost configs and in `.htaccess` itself:

- `AuthUserFile /etc/apache2/.htpasswd` inside a looted vhost is a map to the hash store; the file yields `$apr1$……` (MD5-crypt/APR1) or bcrypt lines — feed john/hashcat with rockyou. A real chain went vhost-read → `AuthUserFile` path → world-readable `.htpasswd` → cracked hash → panel login.
- Reading a protected directory's `.htaccess` frequently exposes the `AuthUserFile` path even when the directory itself is unreachable.
- Any read primitive — an LFI traversal, an XXE `file://` fetch, a template/SSTI file inclusion — should try the `.htaccess` and the `AuthUserFile` it names.

## Version-gated CVEs (upstream-verified)

| CVE | Versions | Conditions | Effect |
|---|---|---|---|
| CVE-2021-41773 | 2.4.49 | non-default config: `<Directory />` missing or `Require all granted`; path-traversal chars partially URL-encoded (`.%2e/`) | file disclosure outside docroot/alias roots; **RCE when mod_cgi enabled** (POST to a cgi-enabled traversed binary) |
| CVE-2021-42013 | 2.4.50 | same, fix bypassed by double-encoding (`.%%32%65/`) | same; fixed in **2.4.51** |

Both come from the path-normalization change in 2.4.49 (`ap_normalize_path` decoding one character at a time). The banner alone is not enough: 2.4.49/50 banners with the default `Require all denied` are NOT vulnerable — confirm the config condition from a looted `<Directory>` block before reporting. These are upstream-history context, not engagement-derived findings.

## Module and config surface to enumerate

- `mods-enabled/` listing (any file read or foothold): `mod_status` (try `/server-status` — request logs, vhost names, real client IPs), `mod_cgi`/`cgid` (execution tier for the CVE pair above), `mod_userdir` (`/~user/` maps to home dirs — real chains gained a foothold by uploading into a user's `public_html` via SFTP and loading it through userdir), `mod_rewrite` (logic the app depends on).
- `a2enmod`/`apache2ctl` in sudo rules or writeable configs = module loading as a primitive.
- `sites-available/` vs `sites-enabled/`: the disabled twin files hold the secrets of vhosts that were "removed" (read them anyway).
- Webroot inference: on a default distro Apache, the docroot is `/var/www/html` — knowing the daemon is Apache turns any blind write/read primitive into a path-guessable one (real chains selected the upload path `/var/www/html/...` purely from the banner).

## Where you'll meet it

- **Classic hosting, CMSes, and panels** — WordPress/Drupal-era boxes, university and internal portals, anything where `.htaccess` support is a feature, not a bug.
- **SFTP+webserver combos**: user homes served via `public_html`/userdir, where the ONLY upload is `.htaccess`-adjacent.
- **As the "invisible" tier**: Apache behind a proxy or as a side service (port 8080-class), still executing its own `.htaccess` logic while the front takes the attention.
- **Appliance and legacy fleets** pinned on old 2.4.x — the version-CVE pair above plus unpatched module sets.

## Reading httpd's mind

1. **A write into a servable dir is config write** — try the junk-header `.htaccess` probe first, then escalate (expression file read → AddType execution → php_value).
2. **`Header ... expr=` is a file-read oracle** — `expr=%{base64:%{file:...}}` exfiltrates through response headers with zero code execution.
3. **`AuthUserFile` paths in any looted config or `.htaccess` are the next file to read** — hashes crack offline.
4. **`.htaccess` behind a non-Apache front does nothing** — vendor-shipped deny files silently vanish (see the [[web-server|router]] dispatch); test the directory directly.
5. **Banner+config condition, not banner alone**, gates the 2.4.49/50 traversal pair.

## Where to go next

- [[web-server|Web servers router]] — daemon dispatch, the any-file-read→config-pull pattern, nginx/Caddy sections.
