---
technology: WordPress
kind: overview
audience: offensive-security
family: web-platforms
layer: PHP web application platform (themes, plugins, REST + XML-RPC APIs, wp-config.php secrets)
language: PHP
first-released: 2003
license: GPLv2+
homepage: "https://wordpress.org"
github-repo: "WordPress/WordPress"
evidence: "14 files (command-context gate: wpscan invocations, wp-content/wp-includes/wp-login.php/xmlrpc.php/wp-config.php paths, wp-json REST calls; the placeholder's 19 was file-level counting — 14 unique files)"
status: enriched
provenance: "mined from 14 evidence files + upstream-verified: WPScan user documentation (GitHub wiki), Kali wpscan tool page, NVD/Rapid7 CVE-2022-0739, Bitnami/stackharbor XML-RPC references, accessed 2026-11-12"
---

# WordPress

> A PHP runtime whose entire security posture is a filesystem convention: plugins and themes are PHP files under predictable `/wp-content/` paths, versions are printed in `readme.txt` and stylesheet headers, and one config file — `wp-config.php` — holds the database credentials. Enumerate the filesystem, match versions, read the config: that is the WordPress methodology.

## What it is

WordPress (2003, Matt Mullenweg / Automattic-led open-source project, PHP, GPLv2+) is the dominant web CMS. An install is: core files, a `wp-config.php` (DB credentials, salts, sometimes debug keys), `wp-content/plugins/*` and `wp-content/themes/*` (third-party PHP, versioned in `readme.txt` / file headers), `wp-admin/` behind `/wp-login.php`, and two APIs — the modern REST API under `/wp-json/` and the legacy XML-RPC endpoint at `/xmlrpc.php`.

The operator's mental model: a plugin marketplace bolted onto a PHP interpreter. Every attack in the evidence base reduces to one of three moves — **enumerate** (which plugins/themes exist, at which versions), **match** (known CVE or plugin-source bug for exactly that version), **read** (`wp-config.php` via LFI/backup/editor-droplets or post-shell, yielding DB credentials). The framework's guarantees stop at the PHP file boundary: any way to place or include a `.php` file under `wp-content/` is code execution, because the web server executes that directory rather than serving it.

The takeaway: WordPress itself is rarely the bug; the long tail of unmaintained plugins is. And the evidence base shows the counter-move too — one site's admin hand-edited the plugin's `readme.txt` version specifically to poison automated enumeration, so version claims from a single source are unverified claims.

## The anatomy that matters on an engagement

    recon (robots.txt /wp-admin, footer, wp-content paths in page source)
      → enumerate: wpscan (-e ap/vp, at/vt, u; --plugins-detection aggressive) / manual readme.txt, /wp-json/wp/v2/users
      → version match: plugin/theme version vs upstream latest → CVE hunt (needs the exact version)
      → exploit class: LFI/RFI in plugin php, unauth SQLi via admin-ajax.php, file upload under wp-content/uploads, RCE in theme/plugin php
      → or auth: wp-login.php / wpscan -e u --passwords / xmlrpc system.multicall
      → wp-config.php → DB credentials → database pivot (often another unit in this vault)

Three structural properties drive most findings:

1. **Predictable paths, versions on the outside.** `/wp-content/plugins/<name>/readme.txt` (Stable Tag), `?ver=` query strings on CSS/JS (observed: `give.css?ver=3.14.0`), generator meta tags — the entire version surface is readable without auth.
2. **Plugins are unauthenticated PHP endpoints.** `admin-ajax.php` `action=<plugin>_...` handlers run for logged-out users when the plugin forgets the `is_user_logged_in()` gate (observed: bookingpress SQLi); `require $_REQUEST['abspath']`-style includes are RFI/LFI (observed: gwolle-gb `abspath=`, brandfolder `wp_abspath=`, wp-with-spritz `url=`).
3. **Secrets concentrate in one file and its droplets.** `wp-config.php` (DB host/user/pass, salts), plus the backups nobody cleans: `wp-config.php.bak`, editor `.save` files (nano), backup directories, config-backup enum classes in wpscan.

## Where you'll meet it

- **Blog/marketing vhosts on the target's main domain** — the classic foot in the door; frequently maintained by a different (less security-aware) team than the main app.
- **Subdirectory installs** (`/wp/`, `/blog/`, `/webservices/wp/`) — easy to miss in scoping; robots.txt `Disallow: /wp-admin` is the tell (observed).
- **Containerized WordPress (bitnami paths)** — `/opt/bitnami/wordpress/`, wp-cli available in-pod; post-shell environment differs (no system packages), loot is the same (observed).
- **Aged boxes stuck on 2015–2022 plugin versions** — the richest ground; the version *gap* between installed and latest (wpscan prints it) is proportional to exploit likelihood.

## Reading the framework's mind

- wpscan's detection modes (`passive`, `mixed`, `aggressive`) trade noise for coverage: aggressive found plugins passive missed (observed: `--plugins-detection aggressive` → buddyforms 2.7.7 at 80% confidence via Known Locations); version numbers come with a confidence % and a "Found By" line — read both.
- The `--api-token` switch is what turns wpscan's version output into CVE output (WPScan Vulnerability Database lookup at scan time); without it you get versions and "out of date" flags but no vuln list — bring your own CVE matching (observed: asgaros-forum 1.15.12 vs latest 2.0.0 matched and searched manually).
- Version sources can disagree: `readme.txt` Stable Tag is *editable by the site owner* (observed — admin set a fake version; real one was in the plugin source/header). Cross-check a second source before ruling a CVE in or out.
- `/wp-json/wp/v2/users` returns the user list on default-permalink installs (observed); wpscan's `u` enumeration and `--passwords` drive wp-login attempts; XML-RPC's `system.multicall` batches hundreds of credential guesses into one POST when wp-login is rate-limited (upstream: enabled by default since 3.5, the most-attacked endpoint on stock installs).
- `wp-config.php` defines `DB_NAME/DB_USER/DB_PASSWORD` and `$table_prefix` — post-shell or LFI, it converts a web finding into a database unit (mysql/postgres siblings in this vault).

## Where to go next

[[wordpress-bug-bounty|WordPress bug-bounty methodology]] — phased: fingerprint → wpscan/manual enumeration → version verification → plugin exploit classes with observed payloads → auth surface (wp-login, XML-RPC) → wp-config.php read paths → tool mapping.
