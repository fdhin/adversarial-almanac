---
technology: Drupal
kind: overview
audience: offensive-security
family: web-platforms
layer: PHP CMS/framework (modules + themes, /sites/default/settings.php secrets, CHANGELOG.txt version oracle, PHP-filter code execution)
language: PHP
first-released: 2001 (Dries Buytaert / Drupal project)
license: GPLv2+
homepage: "https://www.drupal.org"
github-repo: "drupal/drupal"
evidence: "2 writeups (command-context gate: Drupalgeddon exploit invocations against the target, CHANGELOG.txt version-gating lines, /sites/default/settings.php reads, Drupal N.M product banners in attack prose; placeholder's 3 dropped on adjudication — one bare app-list mention 'Apps including Wordpress, Drupal and Joomla', and one file with a single passing 'Drupal CMS.' line on a GitLab host). Thin unit (2 = create floor): corpus supplies the two canonical chains; CVE history verified upstream."
status: enriched
provenance: "corpus-mined from 2 writeups in the writeup corpus + upstream-verified 2026-11-12 against drupal.org advisories directly: SA-CORE-2018-002 = CVE-2018-7600 'Drupalgeddon2' (affected >=7.0 <7.58 || >=8.0.0 <8.3.9 || >=8.4.0 <8.4.6 || >=8.5.0 <8.5.1); SA-CORE-2018-004 = CVE-2018-7602 'Drupalgeddon3' (>=7.0 <7.59 || >=8.0.0 <8.4.8 || >=8.5.0 <8.5.3); SA-CORE-2019-002 = CVE-2019-6339 (phar:// deserialization, 7.x<7.62, 8.5.x<8.5.9, 8.6.x<8.6.6); SA-CORE-2019-003 = CVE-2019-6340 (REST/field sanitization RCE, 8.0.0-8.5.10, 8.6.0-8.6.9, needs rest/JSON:API/Services module); immunIT/drupwn repo (enum/exploit modes)."
---

# Drupal

> The CMS whose 2018 "Drupalgeddon" family set the template for every pre-auth RCE hunt since: check `CHANGELOG.txt` for the exact build, and if it predates 7.58 the site is a public exploit away from a shell — while an admin session plus the PHP-filter module achieves the same thing through the front door.

## What it is

Drupal (2001, Dries Buytaert, PHP, GPLv2+) is the CMS governments and enterprises pick when WordPress feels too casual: a module/theme architecture on a strict "hooks" core, per-site state under `/sites/<sitename>/` (default: `sites/default/` — `settings.php` holds the database credentials, `files/` holds user uploads), and a version discipline where security releases are announced as **SA-CORE-YYYY-NNN** advisories with exact affected ranges. Two artifacts matter more than any scanner: **`CHANGELOG.txt`** in the web root — present on default installs, its first line names the exact core version (the pre-Drupalgeddon check par excellence), deleted on hardened sites; and **`sites/default/settings.php`** — the `wp-config.php`-equivalent loot file (the mysql unit's next target).

The operator's mental model: **Drupal engagements are version-gated exploit selection, not exploration**. The 2018 family alone gives three pre-auth RCE gates: Drupalgeddon2 (SA-CORE-2018-002 / CVE-2018-7600 — "multiple subsystems", Form API + render-array abuse, fixed 7.58/8.5.1 era), Drupalgeddon3 (SA-CORE-2018-004 / CVE-2018-7602 — the follow-up two months later, fixed 7.59/8.5.3), and the naming mistake to avoid: **SA-CORE-2019-002/-003 are the 2019 phar-deserialization (CVE-2019-6339) and REST-field (CVE-2019-6340) issues, not "Drupalgeddon3"** — press shorthand blurs these; the advisory IDs do not (verified on drupal.org this pass, correction of the tasking hint). When the core is patched (observed: one target on 7.58 — the exact Drupalgeddon2 fix version), the chain moves to module-level abuse: admin credentials from anywhere (reused passwords, decrypted backups) → enable the **PHP filter** module (ships disabled in core 7, contrib in 8+) → post PHP in node content → code execution as the web user.

Second takeaway: Drupal's own tooling is part of the attack surface story — `drush` (the CLI) manages users/modules from the shell once you land, and drupwn (immunIT) automates the enum/exploit split the same way droopescan-class scanners always have. The field used manual version checks and public exploit repos; scanners are upstream context.

## The anatomy that matters on an engagement

    fingerprint: Drupal meta/generator, /misc/drupal.js-class paths, login form, "Drupal N.M" banner
      → version: CHANGELOG.txt (first line) — 403/404 means hardened → module/theme banners
      → gate: <7.58 → Drupalgeddon2 (SA-CORE-2018-002); <7.59 → Drupalgeddon3 (-004);
              2019 issues need phar paths or rest/JSON:API — check module presence
      → pre-auth RCE via public exploit (drupalgeddon2.rb class) → shell as www-data
      → or admin by any means → enable PHP filter module → PHP in content = RCE
      → loot: sites/default/settings.php (DB creds) → mysql pivot; #users table hashes → reuse

Three structural properties drive most findings:

1. **`CHANGELOG.txt` is the version gate.** One GET decides whether the pre-auth RCE family applies (observed: one chain read it, saw 7.58 = the Drupalgeddon2 fix release, and did not fire the exploit — the negative gate that saved the engagement; another's Drupal 7 pre-7.58 fell to the public exploit).
2. **Admin + PHP filter = RCE, documented behavior.** The PHP filter module exists to execute PHP in content; core-disabled but one checkbox from enabled with admin rights (observed: enabled post-login in one chain). On 8+ it moved to contrib — its *presence* in the module list is itself an audit finding.
3. **Secrets concentrate in `sites/default/settings.php`.** Post-shell or post-LFI read: `databases['default']['default']['username'/'password']` (observed: both engagements pivoted on it — one into MySQL for the reusable user hash, the other for the H2-adjacent chain).

## Where you'll meet it

- **Public-sector, NGO, and university sites** — Drupal's home turf; long-lived installs where a 7.x site never made the 8/9/10 jumps (the entire Drupalgeddon family applies).
- **Intranet portals behind other footholds** — one observed shape: FTP-exposed encrypted backup cracks to the admin password; the CMS itself was fully patched. Drupal intranets hold org charts and `settings.php` creds worth the detour.
- **Multi-service legacy stacks** — Drupal 7 beside H2/Java consoles on old boxes (field-observed); the Drupal shell is the first foothold, not the target.
- **Post-shell on any web box** — `find / -name settings.php`-class checks; Drupal's `files/` upload tree also serves as webroot-adjacent storage for staged payloads.

## Reading the framework's mind

1. **The pre-auth RCE gates are exact and cheap to check** — read `CHANGELOG.txt` before firing anything: `<7.58`/`<8.5.1` (with the 8.3.9/8.4.6 minors) = Drupalgeddon2; `<7.59`/`<8.5.3` = Drupalgeddon3; the 2019 advisories add the phar (`<7.62`/`<8.6.6`) and REST (`<8.5.11`/`<8.6.10` + a web-services module) gates. All four verified above.
2. **Public exploit repos carry the chain** — the field cloned a Drupalgeddon2 ruby exploit and pointed it at the base URL (`ruby drupalgeddon2.rb http://example.com/`); these tools drop a PHP webshell through the same form/render path and hand you `www-data` immediately.
3. **Registration and password reset behavior tells you the era** — broken mail verification blocking self-registration (field-observed) pushes you to the version-gated pre-auth path instead of account abuse.
4. **`settings.php` converts a web shell into a database unit** — read it first, connect with the mysql unit's workflow, dump `users` (bcrypt `$S$`-prefixed Drupal hashes — phpass), crack and reuse.
5. **`drush` on the box is your admin CLI** — `drush upwd admin «password»`-class commands reset credentials and toggle modules without the web UI when you land as a shell user with rights (absent on observed boxes — checked and noted as missing in one engagement; use it when present).

## Where to go next

[[drupal-bug-bounty|Drupal bug bounty methodology]] — version oracles, the Drupalgeddon2/3 gates with verified ranges, module-era abuse (PHP filter), settings.php/`#users` loot, and scanner context (drupwn, droopescan-class) as upstream tooling.
