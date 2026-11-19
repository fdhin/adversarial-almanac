---
technology: "Web servers (HTTP daemon tier)"
kind: overview
audience: offensive-security
family: web
layer: HTTP server daemon / reverse proxy (TCP/80, 443, 8080-class)
status: area-router
provenance: "evidence-grounded (50 writeups gated at command-context: nginx 19, apache 10, generic config-disclosure 16, caddy 2, other daemons 3 — see ../internal coverage ledger) + upstream-verified: nginx docs (alias/merge_slashes/autoindex), Apache httpd docs (AllowOverride, mod_headers, expr functions), Apache httpd vulnerability advisories (CVE-2021-41773/42013), nostromo advisory (CVE-2019-16278), Caddy reverse_proxy docs. Pass 2 new-unit wave C, 2026-11-12."
---

# Web servers (the daemon tier)

> The first question of every web engagement is not "what app is this" but "what daemon is answering" — because the daemon tier has its own bug classes (alias traversals, `.htaccess` execution, default-vhost leaks) that live entirely below the application.

## What it is

This page is the ROUTER for the HTTP daemon tier: the programs that terminate TCP/80/443-class listeners, speak HTTP, map URLs to files or upstream services, and enforce (or fail to enforce) the first layer of access control. The application behind the daemon changes per engagement; the daemon's mistake patterns repeat with startling fidelity across every network you will touch.

The dominant implementations are **nginx** (event-driven web server and reverse proxy — the front door of most modern deployments), **Apache HTTP Server** ("httpd", process-based with a per-directory config model that creates its own write-attack surface), and a tail of smaller daemons: **Caddy** (Go, automatic-TLS, increasingly common as a self-configuring front), plus relics like **nostromo nhttpd** and **OpenBSD httpd** that survive on forgotten hosts and carry their own CVEs. Implementation-specific surface is on the child pages: [[web-server-nginx|nginx]] and [[web-server-apache|Apache httpd]].

## The engagement's first question: WHICH daemon is this?

Dispatch before you attack — bug classes do not transfer between daemons, and a protection that exists in one is silently absent in the next.

| Signal | nginx | Apache httpd | Caddy | small daemons |
|---|---|---|---|---|
| `Server` header | `nginx` or `nginx/1.x.y` (version hidden only if `server_tokens off`) | `Apache/2.4.41 (Ubuntu)` — distro tag included | `Caddy` — **no version by default** | nostromo: `nostromo 1.9.x` in banner/changelog hints; OpenBSD httpd: `OpenBSD httpd` |
| Default landing page | "Welcome to nginx!" (follows `nginx.conf` edit note) | Ubuntu "Apache2 Ubuntu Default Page" / CentOS test page (the page itself names its config file) | Blank 200 with no version | nostromo ships a page that can be mistaken for a generic default |
| Config files | `/etc/nginx/nginx.conf`, `conf.d/*.conf`, `sites-enabled/*` | `/etc/apache2/apache2.conf`, `sites-{available,enabled}/*`, `.htaccess` (per-directory!) | `Caddyfile`, `/etc/caddy/` | `nhttpd.conf` (nostromo), `/etc/httpd.conf` (OpenBSD httpd) |
| Characteristic bug class | `location`/`alias` path confusion ("off-by-slash"), proxy mapping mistakes | `.htaccess` abuse when uploads land in servable dirs, module-enabled RCE | default pass-through proxying of encoded paths | single-daemon traversal CVEs (e.g. nhttpd ≤1.9.6, CVE-2019-16278, directory traversal to RCE in `http_verify`) |
| Ignores `.htaccess`? | **yes** — protections written for Apache silently vanish behind an nginx front | no — httpd re-reads it per request | yes | yes |

Two dispatch facts earn their keep repeatedly:

- **A protection can depend on the daemon honoring it.** Software packages ship hardening as an Apache `.htaccess` file (deny rules for their data directories). Front the same application with nginx or Caddy and the file is dead weight — the restriction simply is not enforced. Probe "protected" directories directly instead of trusting the vendor's assumptions; this mismatch has produced unauthenticated upload-to-RCE in real deployments (the application's own upload CVE then lands with nothing in front of it).
- **Server banners lie in both directions.** Distro landing pages and copied templates advertise the wrong daemon; the config-file path named in the default page is the honest witness (an nginx default page that says "edit /etc/nginx/nginx.conf" is an nginx box even if the `Server` header was rewritten).

## The area-common pattern: config disclosure through any file read

The most repeated daemon-tier attack pattern on real engagements is not a CVE — it is the **config pull**. The moment you hold ANY file-read primitive (LFI, XXE with `file://`, SQLi `load_file()`, an SSRF that speaks `file://`, a backup or git directory), point it at the web server's own configuration before anything else. Daemon configs are written by administrators, not developers, and they consistently contain the next step of the chain:

```
nginx:    /etc/nginx/nginx.conf
          /etc/nginx/sites-enabled/*        (every vhost: server_name, root, alias,
          /etc/nginx/conf.d/*                proxy_pass targets, auth_basic lines)
          /etc/nginx/conf.d/.htpasswd        (Basic-auth hashes left world-readable)

apache:   /etc/apache2/apache2.conf
          /etc/apache2/sites-enabled/*       (DocumentRoot per vhost, alias/module flags)
          /etc/apache2/sites-available/*     (the disabled twin often holds the secret vhost)
          /etc/apache2/mods-enabled/         (what is actually loaded)
          .htaccess in any servable dir      (application's own access rules)
          /etc/apache2/.htpasswd

caddy:    /etc/caddy/Caddyfile               (routes, reverse_proxy upstreams, tls app identity)

any:      the landing page of a default vhost (names its own config path)
```

What the pulled config reliably buys you:

1. **Hidden vhosts.** `server_name` entries and `DocumentRoot` paths enumerate internal hostnames to add to `/etc/hosts` and fuzz — admin panels and dev tiers that the public DNS never mentions.
2. **Upstream inventory.** `proxy_pass`/`reverse_proxy` lines expose the interior service map (application servers, databases-as-HTTP, monitoring ports) reachable once you can pivot.
3. **Credential material.** `AuthUserFile`/`.htpasswd` paths are cracked offline (`$apr1$` MD5-crypt hashes fall to john/hashcat wordlists); Basic-auth pairs guard admin panels.
4. **The misconfiguration itself.** An `alias` without its trailing slash, an `autoindex on`, an overly-permissive `<Directory>` block — you read the bug before you trip it blindly. Read the nginx and apache pages for the exact directive patterns to grep for in a looted config.

A real deployment of this pattern: a SQL injection used to `load_file()` the default vhost file, which named an internal-only site whose own config then revealed the app server port to pivot to. Treat "read the daemon's config" as the second move of any file-read foothold, immediately after credential files.

## Caddy (section)

Caddy is the Go-written server/proxy whose selling point is configuration-free HTTPS — rare as an internet-facing front today, and therefore thinly understood by most teams.

- **Fingerprinting**: `Server: Caddy` with **no version string** — treat the banner as identification only; version gates are usually impossible from the outside. Admin API listens on `localhost:2019` by default and leaks route/config JSON to anything that can reach it locally (SSRF target).
- **Default proxy pass-through.** By default `reverse_proxy` forwards the request path as received and only touches `X-Forwarded-*` headers. Percent-encoded traversal sequences (`..%2f`) are not normalized away for the upstream. In a real chain, an application redirected users to a generated export path (an HTTP `Location` header built from a fixed prefix plus a filename); rewriting the redirect target to `/export/..%2f..%2f..%2f/etc/passwd` walked the prefix off its directory and read arbitrary files, because the app validated the path only in its own terms and the proxy passed the encoded traversal through untouched. **There is no CVE for this** — it is Caddy's documented default behavior interacting with an application that assumes the front tier normalizes paths. Probe any Caddy-fronted app that reflects or redirects paths with encoded traversal; also remember Caddy ignores `.htaccess` (see the dispatch note above).
- **Where you'll meet it**: hobby-to-mid production fronts, self-hosted dashboards, anything where "automatic HTTPS" sold the deployment. Its config simplicity is exactly why its default pass-through behavior reaches upstreams unfiltered.

## Other daemons worth knowing (section)

- **nostromo nhttpd** (≤1.9.6): directory traversal in `http_verify` → RCE (CVE-2019-16278, fixed 1.9.7; KEV-listed). Config is `nhttpd.conf`; home dirs served per-user (`/~user/`). Old BSD-flavored boxes and appliances.
- **OpenBSD httpd**: `/etc/httpd.conf`, ships with OpenBSD base. Minimal surface; its interest is usually as the visible tier in front of a slower-changing back end. Config reads enumerate the `relay`/fastcgi back ends.

## Where you'll meet it

- **Everywhere, as the outermost tier** — but the daemon's OWN surface matters most when the app behind it is boring: static sites, default pages, and file servers are where administrators lean on daemon config alone, and where alias/autoindex/`.htaccess` mistakes become the whole engagement.
- **Reverse-proxy fronts** (nginx, Caddy) hiding application servers: the front tier's path handling and header policy decide what the back end ever sees — normalize nothing, and the back end's own checks are bypassable through the front.
- **Shared/legacy hosting** where Apache's per-directory model meets user uploads: the `.htaccess` write target is unique to this daemon and uniquely powerful.
- **Forgotten appliance tiers**: small daemons with single CVEs and no maintainer.

## Reading the daemon's mind

1. **Banner first, config-path second, trust neither alone.** Default landing pages name their config file; rewritten `Server` headers do not update the page text.
2. **Any file read becomes daemon recon.** Pull `sites-enabled/*` before `/etc/passwd`-fishing — vhosts and upstreams are worth more than a hash list.
3. **Protections are daemon-relative.** `.htaccess` deny rules, Basic auth on internal vhosts, and path normalization all silently depend on the daemon actually running them; a different front tier voids them.
4. **The daemon tier's bugs are CONFIG bugs, not version bugs** (with narrow exceptions like nhttpd's traversal and httpd's 2.4.49/50 pair) — grep looted configs for the directive patterns on the child pages instead of banner-matching against CVE lists.

## Where to go next

- [[web-server-nginx|nginx attack surface]] — off-by-slash alias traversals, `merge_slashes` and path normalization, root/alias confusion, autoindex and static-serving leaks.
- [[web-server-apache|Apache httpd attack surface]] — `.htaccess` as the write target (mod_headers expression file reads, PHP handler abuse), `.htpasswd` cracking, module-enabled RCE, the 2.4.49/50 traversal pair.
