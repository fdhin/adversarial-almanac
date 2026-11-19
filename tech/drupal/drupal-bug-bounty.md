---
technology: Drupal
kind: bug-bounty-methodology
audience: ai-agent
version: 1.1
family: web-platforms
evidence: "2 writeups (see ../internal coverage ledger) — thin unit, upstream-heavy by design"
source: "rewritten from KathanP19/HowToHunt (CMS/Drupal.md), GPL-3.0, accessed 2026-11-13"
provenance: "corpus-mined from 2 writeups in the writeup corpus + upstream-verified 2026-11-12 directly against drupal.org advisory pages: SA-CORE-2018-002 (CVE-2018-7600, Drupalgeddon2, affected >=7.0 <7.58 || >=8.0.0 <8.3.9 || >=8.4.0 <8.4.6 || >=8.5.0 <8.5.1), SA-CORE-2018-004 (CVE-2018-7602, Drupalgeddon3, >=7.0 <7.59 || >=8.0.0 <8.4.8 || >=8.5.0 <8.5.3), SA-CORE-2019-002 (CVE-2019-6339 phar stream wrapper, 7.x<7.62, 8.5.x<8.5.9, 8.6.x<8.6.6), SA-CORE-2019-003 (CVE-2019-6340 field-type sanitization RCE, 8.0.0-8.5.10, 8.6.0-8.6.9, requires rest/JSON:API/Services module), plus CVE-2019-6339's cve.org record and immunIT/drupwn README (enum/exploit modes). NOTE: tasking hint mapped 'Drupalgeddon3' to SA-CORE-2019-003 — WRONG per drupal.org: -2019-003 is CVE-2019-6340 (REST/field RCE); Drupalgeddon3 is SA-CORE-2018-004 / CVE-2018-7602. Corrected on both pages. HTH INGEST 2026-11-13: node-ID fuzzing technique rewritten from KathanP19/HowToHunt (CMS/Drupal.md), GPL-3.0, accessed 2026-11-13."
---

Companion page: [[drupal|Drupal overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Drupal Bug Bounty Methodology

**Scope:** Drupal core — version oracles, the pre-auth RCE family (Drupalgeddon2/3 and the 2019 advisories), module-era abuse (PHP filter), and the credential loot chain (`settings.php`, `users` table). **Out of scope:** the MySQL server (mysql unit), generic PHP upload bugs in contrib modules beyond noting the surface, LDAP-integration specifics (ldap unit).

**Authorization:** presume written permission. The pre-auth RCE exploits are weaponized webshells — on production, demonstrate the vulnerable response (a version-gated, non-destructive proof) rather than dropping shells unless explicitly allowed; PHP-filter abuse modifies content — restore after proof.

## Decision tree (one screen)

1. Drupal confirmed (meta/paths/login) → Phase 1 (exact version).
2. Version < 7.58 / < 8.5.1-minors → Phase 2 (Drupalgeddon2); < 7.59 / < 8.5.3 → Phase 2 (Drupalgeddon3).
3. 8.x with rest/JSON:API enabled, < 8.5.11 / < 8.6.10 → Phase 2c (CVE-2019-6340).
4. Core patched → Phase 3 (admin by any means: reuse, backup leaks, cracks) → Phase 4 (PHP filter = RCE).
5. Shell or file read → Phase 5 (settings.php → mysql; users table → hashes → reuse).

## Phase 1 — Fingerprinting and the version oracle

```bash
curl -s http://example.com/ | grep -iE 'drupal|generator'     # Drupal meta/scripts, /misc/ or /core/ asset paths
curl -s http://example.com/CHANGELOG.txt | head -3            # "Drupal 7.58, ...-xx (date)" — the oracle
curl -s http://example.com/sites/default/settings.php         # usually executes blank; LFI/file-read needed to see it
```

`CHANGELOG.txt` first line names the exact core build (one observed target: "This installation is 7.58, which is patched"). Hardened installs delete it → fall back to module/theme version files (`modules/*/`/`sites/all/modules/*` changelogs, `system.info`-class files), `generator` meta on older cores, and error-page signatures. Registration state and password-reset behavior are era tells (one observed target's broken mail verification ruled out self-registration).

Node-ID fuzzing belongs in the same first pass: Drupal serves every node at `/node/<N>`, and iterating a few hundred IDs with a fuzzer (Intruder-class) routinely surfaces unlinked test, staging, and dev nodes that no menu, sitemap, or search engine references — hidden administrative helpers and draft content included. Cheap, version-independent, and it enumerates content whose only protection is that nothing links to it.

## Phase 2 — Pre-auth RCE family (gates verified on drupal.org)

| Advisory | CVE | Name | Affected (drupal.org) | Notes |
|---|---|---|---|---|
| SA-CORE-2018-002 | CVE-2018-7600 | Drupalgeddon2 | >=7.0 <7.58 \|\| >=8.0.0 <8.3.9 \|\| >=8.4.0 <8.4.6 \|\| >=8.5.0 <8.5.1 | "multiple subsystems"; form/render-array abuse |
| SA-CORE-2018-004 | CVE-2018-7602 | Drupalgeddon3 | >=7.0 <7.59 \|\| >=8.0.0 <8.4.8 \|\| >=8.5.0 <8.5.3 | follow-up, exploited in the wild alongside -002 |
| SA-CORE-2019-002 | CVE-2019-6339 | (phar deser) | 7.x <7.62 \|\| 8.5.x <8.5.9 \|\| 8.6.x <8.6.6 | phar:// URI ops on user input; usually admin-adjacent |
| SA-CORE-2019-003 | CVE-2019-6340 | (REST field RCE) | >=8.0.0 <8.5.11 \|\| >=8.6.0 <8.6.10 | needs rest module (GET/PATCH/POST) or JSON:API/Services |

Observed chain (Drupal 7 pre-7.58): public Drupalgeddon2 exploit → shell:

```bash
git clone https://github.com/dreadlocked/Drupalgeddon2.git   # field-used repo (exploit families exist for 7600/7602)
ruby drupalgeddon2.rb http://example.com/                    # interactive webshell as www-data
```

Negative gate discipline: 7.58 is *inside* nothing — it is the -002 fix release; do not fire. Report the exact `CHANGELOG.txt` line next to every CVE claim.

## Phase 3 — Admin acquisition when core is patched

Observed sources: an FTP-exposed, OpenSSL-encrypted backup (`.drupal.txt.enc`, `Salted__` header — brute-force the passphrase offline) containing the admin password reused at Drupal (the CMS was patched; the *credential* was the vulnerability); database dumps from earlier pivots. Try reused org passwords and cracked `$S$` phpass hashes (Phase 5) at `/user` before anything exotic.

## Phase 4 — PHP filter module (admin → RCE, documented)

Observed chain: log in as admin → **Modules → enable "PHP filter"** → create/edit content (node/page) with input format *PHP code* → payload executes as the web user. Also worth enabling-check: a `php` module already present in the module list (droopescan-class enumeration flagged exactly that). On Drupal 8+ the PHP filter moved out of core to contrib — finding it installed is an audit finding by itself. Post-admin alternatives that need no module: `drush` if present on the host (user resets, module toggles — observed box lacked it, check anyway), theme/template file edits where the install allows writes.

## Phase 5 — Loot chain

- **`sites/default/settings.php`** — `databases['default']['default']` block: host/user/password → connect per the mysql unit (one engagement dumped the `users` table this way; the other read the same file for its chain).
- **`users` table** — Drupal phpass `$S$` hashes; crack (john/hashcat rockyou-class) and reuse on SSH/host accounts (observed: a user hash → SSH → snap-based privesc).
- **`sites/default/files/`** — uploaded content tree; staged payloads and often listable backup droplets.

## Disclosure notes

- Every CVE claim carries its gate: quote `CHANGELOG.txt` (or the fallback version source) + the advisory ID/range from the table above. The name↔advisory mapping in press coverage is unreliable — always cite SA-CORE IDs (see the correction note in provenance).
- Pre-auth RCE proof on production: prefer the advisory's non-destructive detection (version gate + a benign response differential) over webshell deployment; record that the public exploit applies, not just that the version is old.
- PHP-filter abuse = intended-but-dangerous feature; frame as "administrative RCE by design, obtained via <credential path>" and report the credential path as the primary finding.

## Appendix A — Tool mapping (prose)

- **curl** — CHANGELOG.txt/meta fingerprinting, version oracles, response differentials.
- **Drupalgeddon exploit repos** (ruby, public) — weaponized CVE-2018-7600/7602 chains; the field used the Drupalgeddon2 ruby script against a Drupal 7 target.
- **drupwn** (immunIT) — enum/exploit two-mode scanner (`--mode enum` with `--users/--nodes/--modules/--dfiles/--themes`; `--mode exploit` for CVE checks); upstream-verified, zero observed use — treat output as leads to verify by hand.
- **droopescan-class scanners** — module/theme enumeration context (an observed engagement references this class of tool).
- **john / hashcat** — phpass `$S$` user hashes.
- **mysql client** — `settings.php` credential pivot (mysql unit).
- **drush** — Drupal admin CLI on-box (user resets, module toggles) when present.
- **OpenSSL brute tooling** — `Salted__`-header encrypted backups from FTP/rsync exposures feeding Phase 3 (see the ftp unit's encrypted-backup context).
