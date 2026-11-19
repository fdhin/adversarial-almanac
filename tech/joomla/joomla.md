---
technology: Joomla
kind: overview
audience: offensive-security
family: web-platforms
layer: PHP CMS (administrator backend, templates, com_* components, webservice API, configuration.php secrets)
language: PHP
first-released: 2005 (Mambo fork)
license: GPLv2+
homepage: "https://www.joomla.org"
github-repo: "joomla/joomla-cms"
evidence: "3 evidence files (command-context gate: Joomla generator meta/robots signatures, /administrator + manifests version enum, com_*/template paths in attack commands, configuration.php reads; one placeholder candidate dropped — its only hit was an app-detection list 'Apps including Wordpress, Drupal and Joomla'). Thin unit: the evidence supplies the skeleton (version enum → CVE → admin → template RCE); CVE history and tooling upstream-verified."
status: enriched
provenance: "field-mined from 3 engagements in the vault's evidence pool + upstream-verified 2026-11-12: Joomla security-centre advisory [20230201] + NVD CVE-2023-23752 (4.0.0-4.2.7, webservice endpoint improper access check, fixed 4.2.8), Exploit-DB 42033 / Sucuri + Qualys analyses CVE-2017-8917 (com_fields SQLi, Joomla 3.7.0 only, list[fullordering]), OWASP joomscan repo (perl scanner), docs.joomla.org version-detection page (README.txt / language-file version sources)."
---

# Joomla

> A PHP CMS that names itself on every page (`generator` meta), versions itself in reachable XML manifests, and — once you are an administrator — lets you edit the site's PHP templates in the browser. Recon is loud by default; admin is RCE by design.

## What it is

Joomla (2005 Mambo fork, PHP, GPLv2+) is the third-place big CMS: sites built from `com_*` components (each a URL-addressable application), modules, plugins and a **template** (the PHP/skin layer rendered on every page), all administered at `/administrator`. Identity leaks are structural: the `<meta name="generator" content="Joomla! - Open Source CMS">` tag, a `robots.txt` that disallows the CMS's own directories (`/administrator/`, `/components/`, `/modules/`, `/plugins/`, `/templates/`, `/api/` …), and version-bearing files — root `README.txt` (deleted on hardened sites, which is itself a signal), `/administrator/manifests/files/joomla.xml` (`<version>` tag), and per-extension XML manifests in `language/` and `administrator/language/`. Joomla 4+ additionally exposes a **webservice API** under `/api/` (the `com_api`-era routes: `/api/index.php/v1/...`), which is where its most famous recent CVE lives. `configuration.php` in the web root holds the database credentials (the loot file, same role as WordPress's `wp-config.php`).

The operator's mental model: **Joomla is WordPress's architecture with quieter tooling** — fingerprint → exact version → version-gated CVE (usually an information disclosure or SQLi that yields admin) → **administrator → template editor → append PHP to a template file → webshell**. That last step is not a bug: the admin UI ships a PHP template editor, so admin equals RCE and every unauth-to-admin chain is an RCE chain. The evidence shows this exact shape three times, two of them via the same CVE.

The takeaway for thin evidence: the evidence pool has zero joomscan and zero com_fields exploitation — the modern chain (CVE-2023-23752 → admin → template edit) is the field-proven path; the classic pre-4.x era (com_fields SQLi, joomscan) is upstream history, useful when you meet an old 3.x site that never upgraded.

## The anatomy that matters on an engagement

    fingerprint: generator meta | robots.txt disallow pattern | /administrator/ reachable
      → version: /administrator/manifests/files/joomla.xml (<version>) | README.txt if present
      → version-gated CVE: 4.0.0-4.2.7 → CVE-2023-23752 unauth config dump (DB + SMTP creds)
      → /administrator login (CVE creds | reused creds | cracked hash)
      → admin → System > Site Templates > edit template PHP → webshell in template path
      → loot: configuration.php (DB creds) | database #_users table hashes → SSH reuse

Three structural properties drive most findings:

1. **Identity and version are on the outside.** The generator meta, the robots disallow set, and version manifests are all unauthenticated; `/administrator/manifests/files/joomla.xml` is the canonical version oracle on hardened 4.x sites where `README.txt` is deleted (observed). Version → CVE is a two-step lookup.
2. **Admin = template editor = RCE.** `System → Site Templates → <template> Details and Files` edits template PHP inline (observed: Cassiopeia's `error.php` appended with a shell, Protostar's `index.php` given command execution); the shell then lives under `/templates/<name>/...` where the web server executes it.
3. **`configuration.php` is the loot file and the API is its shortcut.** Pre-4.5-style chains read it post-shell (observed: read for DB creds); CVE-2023-23752 reads its *contents* (dbuser, db password, and more) unauthenticated through `/api/index.php/v1/config/application?public=true` — the config dump without the shell.

## Where you'll meet it

- **Marketing/community sites on the main domain** — often a 4.x install a version or two behind; the CVE-2023-23752 window (4.0.0–4.2.7) covered every 4.x release for two years, so unpatched installs are common.
- **Subdirectory or subdomain installs** (`/joomla/`) on Windows/legacy estates (observed: Joomla beneath a Windows AD environment; its creds fed a broader chain).
- **Ancient 3.x sites** — the com_fields SQLi (CVE-2017-8917, 3.7.0 exactly) and joomscan-era issues live here; a 3.x site is usually also missing every later hardening.
- **Post-shell on the box** — `configuration.php` in the web root gives the MySQL pivot (mysql unit); the `#_users` table's bcrypt hashes crack and reuse on SSH (observed).

## Reading the framework's mind

1. **`robots.txt` is a Joomla confession** — the disallow block *is* the directory map: `/administrator/`, `/bin/`, `/cache/`, `/components/`, `/includes/`, `/installation/`, `/language/`, `/layouts/`, `/libraries/`, `/logs/`, `/modules/`, `/plugins/`, `/templates/`, `/api/` (observed in nmap http-robots output). Fuzzing these beats generic wordlists.
2. **Version-gate the API dump precisely** — CVE-2023-23752 affects **4.0.0–4.2.7 only**, fixed in 4.2.8 (verified advisory); a 4.x site reporting `4.2.6` (observed) is inside the window, `4.2.8+` is out.
3. **The unauth config dump returns working credentials** — `/api/index.php/v1/config/application?public=true` yields the database (and often SMTP) username/password in cleartext; try them at `/administrator` before anything harder (observed: `lewis:«password»` → Super User, twice via the same CVE).
4. **Template-edit RCE needs the right file** — `error.php` in the active template is the observed favorite (rendered on error pages, reachable at `/templates/<name>/error.php`-style routes); keep the appended payload minimal and the original file's logic intact so the page still renders.
5. **User hashes are bcrypt in `#_users`** — post-DB access, `$2y$...` hashes crack with rockyou-class lists and are the credential-reuse source for SSH (observed: logan → SSH).

## Where to go next

[[joomla-bug-bounty|Joomla bug bounty methodology]] — fingerprint/version oracles, the CVE-2023-23752 workflow, admin-path acquisition, template-editor webshells, and the 3.x-era history (com_fields SQLi, joomscan) as upstream context.
