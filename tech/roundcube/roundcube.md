---
technology: "Roundcube"
kind: overview
audience: offensive-security
family: web-platforms
layer: self-hosted webmail (PHP IMAP client over org mail infrastructure)
language: php (server), javascript (elastic skin)
first-released: 2005
license: "GPL-3.0-or-later"
homepage: "https://roundcube.net"
github-repo: "roundcube/roundcubemail"
status: thin
provenance: "field-grounded (4 engagements pass the command-context gate over text+ocr, zero exclusions — see internal coverage ledger; chains: version pinned by release-date inference → CVE-2020-12640 plugin-name traversal via the left-in-place installer → file inclusion of an uploaded payload → web shell; IDOR contact form + CVE-2024-42009 desanitization XSS → victim mailbox exfiltration and reset-link harvest; root on the mail host → shadow-hash crack → credentials replayed into the webmail; docker-socket recon exposing a loopback-bound webmail container beside the mailserver) + upstream-verified (NVD + Debian tracker for CVE-2020-12640 ranges incl. fix in 1.4.4 and commit 814eadb; cve.org + SonarSource research + roundcube 1.5.8/1.6.8 release notes for CVE-2024-42009 (and siblings 42008/42010); defaults.inc.php at tag 1.4.2 and master for db_dsnw, des_key, enable_installer, display_product_info, smtp_user %u / smtp_pass %p, 1.x useragent version stamp). Thin unit treated first-class per standing rule. Pass 2 new-unit wave B, 2026-11-12."
---

# Roundcube

> A webmail client with no user database of its own — its login *is* the IMAP login, its outgoing mail *is* the SMTP login, and its installer directory, when left on disk, is a pre-auth file-inclusion primitive.

## What it is

Roundcube (2005, Roundcube Dev Team; GPLv3) is the default self-hosted webmail of small and mid-size organizations: a PHP application that renders an IMAP mailbox in the browser and sends through SMTP. It keeps only a thin SQL database for preferences and contacts — authentication is delegated wholesale to the mail backend, and with `auto_create_user` on (the default) any credential that works against IMAP becomes a webmail account on first login. The 1.x series (still widely deployed) is the offensive sweet spot: PHP file inclusion through the installer-era plugin API, and a 2024 XSS family that turns one crafted email into a victim's whole mailbox.

For an operator the product's defining trait is **credential passthrough**: a Roundcube password is simultaneously the IMAP password, the sending (SMTP submission) password, and — in field practice — very often the shell password of the person who owns the mailbox. Cracking or phishing one opens the mail stack at both ends (see [[pop3|pop3]] and [[smtp|smtp]]).

## The anatomy that matters on an engagement

    browser → roundcube (PHP, 80/443, often a vhost like webmail.*)
                 ↘ auth: IMAP login (default_host localhost:143 → the real mail store)
                 ↘ send: SMTP with smtp_user '%u' / smtp_pass '%p'  (same creds reused)
                 ↘ SQL: db_dsnw DSN in config/config.inc.php (prefs, contacts, sessions)
                 ↘ installer/ : setup wizard, ships in the tree, gated only by a config flag
    plugins/: each entry in $config['plugins'] includes plugins/<name>/<name>.php

Three structural properties drive most findings:

- **The plugin loader includes by name.** Plugin names become filesystem paths; before the 1.4.4 fix, nothing stopped traversal — which is CVE-2020-12640, and the installer is one unauthenticated road into that parameter.
- **Every secret is in `config/config.inc.php`.** The DSN (`db_dsnw`), the mail hosts, and `des_key` — a 24-byte key, shipped with a default value, that encrypts the IMAP password inside the session store.
- **The mailbox is behind the app, not inside it.** XSS in the webmail equals the victim's mail — password resets for every other system they use, sent straight to your exfiltration endpoint.

## Where you'll meet it

- **A webmail vhost on port 443** of a mail-heavy target (field-seen): the login page title is `$config['product_name']` — organizations rebrand it ("Example Webmail"), so identify by URL shape (`/?_task=login`) and assets, not by the name string.
- **Loopback-bound inside a container fleet** (field-seen): `docker ps` from a compromised neighbor shows `roundcube/roundcubemail` published only on `127.0.0.1:8000` beside a full mailserver container — internal-only, reached by exec or proxy, never scanned from outside (see [[docker|Docker]]).
- **Aging one-purpose mail hosts**: old 1.x installs on odd ports, version nowhere on the login page, installer forgotten in the tree — the CVE-2020-12640 profile.
- **Windows front-ends to Linux mail** (field-seen): the webmail is often the only Linux service reachable; landing on its host yields shadow hashes whose cracks replay into the webmail itself.

## Reading the platform's mind

Five facts predict behavior — each expands in [[roundcube-bug-bounty|the methodology]]:

1. **The login page hides the version by default** (`display_product_info` defaults to product-name-only) — but 1.x mail stamped a `User-Agent: Roundcube Webmail/<version>` header on every message a user sent (defaults at the 1.4.2 tag), and the leftover `installer/`, `CHANGELOG`, or `README` files pin it exactly. Field practice even infers the version from release dates when nothing leaks.
2. **`_plugin_<name>` parameters become includes** on pre-1.4.4 builds (CVE-2020-12640, CVSS 9.8, network/no-auth/no-privileges): a traversal name includes any file you can place on disk — the classic pairing is any upload primitive into a writable path such as a shared `/dev/shm`.
3. **One crafted email reads a mailbox on builds through 1.5.7 / 1.6.7** (CVE-2024-42009): the message-rendering pipeline re-processes sanitized HTML and can be broken out of; siblings CVE-2024-42008 (attachment serving) and CVE-2024-42010 (CSS remote content) ship from the same research.
4. **Mail-credential reuse is the whole game**: `smtp_user '%u'`/`smtp_pass '%p'` mean webmail creds send mail too — harvest once, then phish the org from its own trusted domain, or replay against [[pop3|pop3]]/[[smtp|smtp]] and the user's shell account (field-proven in both directions).
5. **`des_key` ships as a default string** (`rcmail-!24ByteDESkey*Str`, 1.4-era default): an unchanged key plus a database read decrypts the IMAP passwords Roundcube parks in its session table — config loot converts straight to mail creds.

## Where to go next

[[roundcube-bug-bounty|Roundcube bug bounty methodology]] — version hunting, the installer/plugin-inclusion exploit with its upload-prerequisite mechanics, the XSS delivery chain (including IDOR-style mail-injection front ends), mailbox post-exploitation, and config/credential loot.
