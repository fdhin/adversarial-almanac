---
name: "snallygaster"
categories:
  - blackarch-webapp
version: "v0.0.15.r5.g1819e27"
homepage: "https://github.com/hannob/snallygaster"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "hannob/snallygaster"
github-stars: 2113
github-pushed: "2026-10-18"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# snallygaster

> Scan web servers for accidentally exposed files — git repos, backups, config dumps — and related footguns.

## What it's for

snallygaster (Hanno Böck) checks HTTP servers for files that should never be public but often
are: exposed `.git` directories, backup files and database dumps that may hold passwords, leaked
credentials, and a set of related misconfiguration checks. Each test is documented in the
upstream TESTS.md with references to the underlying issue. It is a point-and-shoot python3
script that takes hostnames (both `http` and `https` plus the `www.` variant are probed by
default) and prints what it finds — an asset-hygiene sweep, not a general web scanner.

## Typical use cases

### Scan a host for exposed files

```
snallygaster example.com
```

Outcome: the host (plus `www.example.com`, over both HTTP and HTTPS) is probed with every test;
findings print with the vulnerable URL and the issue name, e.g. an exposed git repository or a
world-readable backup file.

### Scan under a base path or an app subtree

```
snallygaster -p /app example.com
```

Outcome: all probes are sent under `/app` instead of the server root, matching applications
deployed under a subpath.

### Run only specific tests

```
snallygaster -t git,dirlist example.com
```

Outcome: only the named checks execute — useful for quick re-verification after a fix or for
running one noisy check without the rest.

### Machine-readable output

```
snallygaster -j example.com
```

Outcome: findings are emitted as JSON records on stdout for ingestion into logging or monitoring
instead of human-readable lines.

No corpus evidence — derived from upstream documentation (README and source argparse).

---

## Bug bounty workflow with snallygaster

snallygaster is a curated **forgotten-file detector**, not a scanner. It carries a fixed
checklist of ~49 default security tests (plus 5 info tests behind `-i`), each one a light,
mostly single-request probe for a specific artefact that should never be web-reachable: an
exposed `.git`/`.svn` working copy, a Laravel `.env`, a framework config with live DB
credentials, an SQL dump, a private key, an unsecured Adminer or Elasticsearch, a leftover
installer. Think of it as "the asset-hygiene checklist a careful hunter would run by hand,
automated and kept current." Its output is a **lead list**, never a confirmed finding — every
hit is a candidate you verify manually before it goes in a report.

The whole design is low-volume by nature: there is **no thread/concurrency flag and no
rate-limit flag**, the per-request timeout is hardcoded at 2 seconds, and the test set is
finite. That makes it one of the safer tools to point at an authorized target — but "safer"
is not "passive": a handful of tests send active payloads (`headerinject`, `phpunit_eval`,
`cgiecho`, `acmereflect`, `citrix_rce`), and the `axfr` test leaves HTTP entirely to attempt a
DNS zone transfer against the host's nameservers. Phase 0 exists to fence those off.

### Phase 0 — Authorization and scope gate

Before the first request, settle four things:

- **Host is in scope, in writing.** snallygaster expands one hostname into up to **four
  origins** — `http://` and `https://` for both the bare host *and* its `www.` variant
  (`example.com`, `www.example.com`). On many programs `www.example.com` is a *different*
  scope line item than the apex; confirm both are in scope or use `--nowww` to stay on the
  one you were granted.
- **Active vs. passive-only rules.** If the program forbids active exploitation, exclude the
  payload-sending tests. The cleanest way is an allowlist: run only the file-presence checks
  with `-t` (see recipes) rather than the full default set.
- **DNS testing is a separate question.** `axfr` queries the target's authoritative
  nameservers for a zone transfer. That is not a web request and is frequently outside a
  web-app program's scope (the nameservers may belong to a third-party DNS provider). Exclude
  it unless DNS is explicitly in scope.
- **Detection ≠ exploitation.** Finding an open Adminer, an Elasticsearch with `admin:admin`,
  an exposed installer, or a `.git` dir is the *finding*. Logging in, running the installer,
  querying the cluster, or cloning the entire repository history are separate, higher-risk
  acts that can exceed your authorization. Plan to prove presence with the minimum request
  count and stop.

### Phase 1 — Baseline and soft-404 calibration

snallygaster's single biggest false-positive source is a server (or CDN/WAF) that answers
**200 for everything** — a catch-all route, a SPA index fallback, a "friendly" error page.
Against such a host nearly every file-presence test fires, because the tool sees a 200 where it
expected a 404. Calibrate first:

```bash
# Does a guaranteed-nonexistent path return 404, or a 200 soft-error?
curl -s -o /dev/null -w '%{http_code}\n' https://example.com/this-does-not-exist-9f3a2b.txt
curl -s -o /dev/null -w '%{http_code}\n' https://example.com/this-does-not-exist-9f3a2b/
```

A clean `404` means snallygaster's hits are trustworthy signals. A `200` (or a `302` to a login
or marketing page) means you must verify *every* hit by hand and expect noise — note it now so
a reviewer does not later wave your report away as a soft-404 artefact.

### Phase 2 — The light sweep

Run the tool against one host at a time, scoped to what Phase 0 allowed, and capture machine
output for triage:

```bash
snallygaster -j example.com | tee example.com.leads.json
```

This is your lead list. Nothing here is a finding yet. For a large asset inventory, loop
**one host at a time with external pacing** — there is no built-in throttle (Phase "Pitfalls").

### Phase 3 — Triage the lead list

Classify each hit by the "Where the money is" table below and drop the obvious noise
(`invalidsrc` warnings, info tests you did not need). Rank what survives by *what the artefact
plausibly contains*, not by the test name: an empty `.env` is Info; a `.env` with a live
production `DB_PASSWORD` is High–Critical. You cannot know which until Phase 4.

### Phase 4 — Manual verification, one request per hit

Every surviving lead gets a single, surgical confirming request — the evidence that turns a
scanner line into a report. Verify the *artefact*, not just the 200:

```bash
# .git exposure — prove it is a live repo, not a 200 catch-all page
curl -s https://example.com/.git/HEAD            # -> "ref: refs/heads/main"
curl -s https://example.com/.git/config          # -> [core] ... [remote "origin"]

# .env — fetch once, confirm real keys, then STOP (redact before storing)
curl -s https://example.com/.env | grep -iE 'APP_KEY|DB_|SECRET|PASSWORD'

# Adminer / installer / data store — confirm the page identity, do NOT authenticate
curl -s https://example.com/adminer.php | grep -i 'Adminer'
```

The discipline: fetch exactly enough to prove the artefact is real and sensitive, redact
secrets immediately, and do not pivot into the system it unlocks. For `.git`, `/.git/HEAD` plus
`/.git/config` proves a downloadable repo without pulling the whole history; reserve a full
`git-dumper` clone for when the program explicitly wants the proof and you have the authorization
to hold that data.

### Phase 5 — Corroborate and de-duplicate

Cross-check against your broader recon (dirsearch, nuclei, ffuf) so you neither double-report nor
miss corroboration, and check the program's known-issues / out-of-scope list — `.git`, `.env`,
and `phpinfo` exposures on mature programs are frequently already filed or explicitly excluded.

### Phase 6 — Report

One finding per artefact, impact-first, with the verifying request/response pair and the
soft-404 baseline attached (Phase "Reporting evidence").

---

## Where the money is with snallygaster

Priority is driven by what the detected file *contains*, which maps to specific tests. Dupe risk
is high for the famous low-hanging fruit and low for the app-specific or default-credential
checks that generic wordlists miss.

| Priority | Finding class | snallygaster test(s) | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Config with live DB/app credentials | `dotenv`, `magento_config`, `rails_database_yml`, `symfony_databases_yml`, `drupal_backup_migrate` | High–Critical | Medium |
| 2 | Private / SSH keys | `privatekey`, `sshkey` | High–Critical | Low |
| 3 | Unsecured data store or DB admin | `adminer`, `openelasticsearch`, `openmonit`, `telescope`, `elmah`, `drupaldb` | High–Critical | Low |
| 4 | Known-CVE RCE / mem-leak endpoints | `phpunit_eval` (CVE-2017-9841), `citrix_rce` (CVE-2019-19781), `optionsbleed` (CVE-2017-9798), `installer`, `duplicator`, `wpsubdir` | Critical (age-dependent) | Low |
| 5 | Exposed source repo | `git_dir`, `svn_dir` | Medium (High if secrets in history) | **High** |
| 6 | Database / server dumps & backups | `sql_dump`, `backup_archive`, `backupfiles`, `coredump`, `bitcoin_wallet` | High | Medium |
| 7 | Default credentials | `ilias_defaultpw`, `openmonit`, `openelasticsearch` | High | Low |
| 8 | FTP/editor credential files | `sftp_config`, `wsftp_ini`, `filezilla_xml`, `winscp_ini` | Medium–High | Low |
| 9 | Header injection / open-redirect | `headerinject` | Medium | Medium |
| 10 | Debug/error-console disclosure | `wpdebug`, `elmah`, `phpwarnings` | Low–Medium | Medium |
| 11 | Metadata / path disclosure | `ds_store`, `thumbsdb`, `desktopini`, `idea`, `php_cs_fixer`, `apache_server_status`, `apache_server_info` | Low–Medium | **High** |
| 12 | Version/info fingerprint (`-i`) | `drupal`, `wordpress`, `mailman`, `composer`, `phpinfo` | Info–Low | High |
| 13 | DNS zone transfer (scope caution) | `axfr` | Medium (info) | Low |

Rows 1–4 are where real payouts live; rows 5 and 11–12 are where you most often collide with an
existing report, so always run Phase 5 de-dup on them first.

---

## Command recipes

Every flag below is from the tool's own argparse (`snallygaster --help`). There is no `--version`,
no `--timeout`, and no concurrency flag; the request timeout is fixed at 2 s internally.

```bash
# Quiet, full default sweep of one host (http+https, bare + www) — human-readable
snallygaster example.com
# -> one line per hit: the test name followed by the vulnerable URL,
#    e.g.  git_dir: https://example.com/.git/
```

```bash
# Machine-readable lead list for triage/parsing
snallygaster -j example.com | tee example.com.leads.json
# -> JSON records on stdout; still a candidate list, still needs manual verification
```

```bash
# Cut request volume / stay on the exact scoped origin:
#   --nowww   skip the www.[host] variant
#   --nohttp  skip plaintext http (test https only)
#   --nohttps skip https
snallygaster --nohttp --nowww example.com      # one origin only: https://example.com
```

```bash
# Passive-only allowlist — run just file-presence checks, exclude active/DNS tests.
# Good when the program forbids active exploitation.
snallygaster -t git_dir,svn_dir,dotenv,privatekey,sshkey,sql_dump,backup_archive,backupfiles,\
adminer,magento_config,rails_database_yml,symfony_databases_yml,ds_store example.com
```

```bash
# Re-verify a single class after a fix, or isolate one noisy check
snallygaster -t git_dir example.com
```

```bash
# App deployed under a subpath — probe under /app instead of the web root
snallygaster -p /app example.com
```

```bash
# Add the 5 info tests (version fingerprints; no vuln implied)
snallygaster -i example.com                    # adds drupal/wordpress/mailman/composer/phpinfo

# Show "noisy" boring-bug messages that are suppressed by default
snallygaster -n example.com
```

```bash
# The default User-Agent is a static, dated Firefox 53 string — a recognizable scanner
# signature and sometimes WAF-blocked. Override it, e.g. to the identifier your program
# asks hunters to send:
snallygaster --useragent "bugbounty-researcher (hackerone.com/yourhandle)" example.com
```

```bash
# Audit exactly which requests go out (scope evidence / debugging a false positive)
snallygaster -d -t git_dir example.com
```

**Low-volume discipline.** With no throttle flag, volume is governed by *how you drive the tool*,
not by a setting. Keep it modest: scan **one host per invocation**; collapse the 4× origin fan-out
with `--nowww` / `--nohttp` when you only hold one origin; use `-t` to run the handful of tests
that matter instead of all 49; and for an asset list, iterate hosts sequentially with your own
`sleep` between them rather than backgrounding dozens at once.

---

## Pitfalls, noise and scope discipline

- **Soft-404 / catch-all = false positives everywhere.** The number-one reason snallygaster
  reports get rejected. A host that returns `200` for any path makes nearly every file-presence
  test fire. Always run the Phase 1 calibration, verify every hit by hand, and attach the 404
  baseline to the report so a triager cannot dismiss it as a soft-404 artefact.
- **The 4× origin fan-out can leave scope.** One hostname becomes `http`/`https` × bare/`www`.
  If `www.example.com` is not in your scope, `--nowww` keeps you inside it; otherwise you have
  sent unauthorized requests to a host you were not granted.
- **`axfr` is not a web test.** It attempts a DNS zone transfer against the authoritative
  nameservers — different protocol, often a third-party provider, usually outside a web-app
  program's scope. Exclude it with a `-t` allowlist unless DNS is explicitly in scope.
- **Some tests send active payloads.** `headerinject` injects a URL-encoded newline/header,
  `phpunit_eval` posts to an eval endpoint, `cgiecho`/`acmereflect` trigger reflection, and
  `citrix_rce` probes a traversal. On a strict "no active exploitation" program these can breach
  the rules of engagement. If unsure, run the passive allowlist recipe.
- **Detection is the finding — do not exploit past it.** Logging into a discovered Adminer,
  authenticating to an open Elasticsearch/Monit with default creds, executing a leftover
  installer, or cloning an entire exposed `.git` history are separate acts that commonly exceed
  authorization and can get you removed from a program. Prove presence minimally; stop.
- **Dated default User-Agent.** The hardcoded Firefox 53 UA is both a scanner fingerprint (WAFs
  block it) and non-compliant with programs that require hunters to identify themselves. Set
  `--useragent` accordingly.
- **High dupe risk on the famous classes.** `.git`, `.env`, `phpinfo`, `.DS_Store`,
  `server-status` are the first things every other hunter and the program's own scanners check.
  De-dup against the known-issues list before reporting (Phase 5); many programs auto-close or
  explicitly scope these out.
- **No throttle; do not loop it hot.** Wrapping snallygaster in a tight loop over hundreds of
  hosts with no delay turns a polite tool into a noisy one and can trip rate-based defenses.
  Pace externally.
- **`invalidsrc` and `-n`/`-i` output are context, not findings.** `invalidsrc` "often produces
  harmless warnings," and the info/noisy tiers describe the target rather than flag a bug. Keep
  them for recon context; don't file them as vulnerabilities.

---

## Pairing with dirsearch and nuclei (overlap and division of labour)

snallygaster, dirsearch, and nuclei all surface "forgotten file" exposures, but they reach them
differently, so running all three blindly wastes requests and buries you in duplicate leads. Know
what each is *for*:

- **snallygaster** — a **fixed, curated checklist** of ~49 specific artefacts, each a hand-written
  check (often one request). Quiet, finite, and it knows odd things a wordlist does not: the exact
  shape of a `sftp-config.json`, a `DEADJOE`, a PHP-CS-Fixer cache, an Ilias default login, the
  Optionsbleed memory leak. It does **not** brute-force arbitrary paths.
- **dirsearch** — a **wordlist brute-forcer**. It finds *any* path in your list, including backups
  and dumps with names snallygaster does not carry, but it is higher-volume and finds nothing it
  was not told to look for.
- **nuclei** — a **template engine**. Its `exposures` / `exposed-panels` / `misconfiguration`
  template tags overlap snallygaster heavily (git config, `.env`, Adminer panels, backups) and add
  CVE templates, but coverage depends on which templates you pull.

| Capability | snallygaster | dirsearch | nuclei |
|---|---|---|---|
| Exposed `.git` / `.svn` | `git_dir`, `svn_dir` | if dir in wordlist | `exposures` git templates |
| `.env` / framework configs | `dotenv`, `*_config`, `*_database_yml` | if path in wordlist | `exposures`/`config` templates |
| Backups & SQL dumps | `backupfiles`, `backup_archive`, `sql_dump` | **strong** (wordlist) | some backup templates |
| Admin panels / data stores | `adminer`, `openelasticsearch`, `telescope`, `elmah` | panel paths only | `exposed-panels` templates |
| App-specific cred files | `sftp_config`, `wsftp_ini`, `filezilla_xml`, `winscp_ini`, `deadjoe`, `php_cs_fixer` | only if in wordlist | rarely templated |
| Active checks (not a path) | `optionsbleed`, `headerinject`, `cgiecho`, `acmereflect` | **no** | some (CVE templates) |
| Default-cred login checks | `ilias_defaultpw`, `openmonit`, `openelasticsearch` | no | some panel templates |
| DNS zone transfer | `axfr` | no | dns templates (separate) |
| Arbitrary path discovery | **no** | **yes** | no |

Practical division of labour: run **snallygaster first** as the fast, quiet baseline — it is the
cheapest way to clear the common-artefact checklist and it catches the app-specific files the other
two miss. Then run **nuclei exposure/panel templates** to corroborate and pick up CVE-tagged
endpoints, and **dirsearch** when you want genuine path brute-force for oddly-named backups. When
two tools report the same artefact (almost guaranteed for `.git`/`.env`), **de-duplicate to one
finding** — filing the same `.git` exposure three times because three tools found it is a fast way
to annoy a triager. snallygaster's unique value in the trio is its curated tail (the credential
files, `optionsbleed`, default-cred checks); lean on that rather than re-reporting the headline
exposures everyone's scanner already flags.

---

## Manual verification cookbook (per class)

Each surviving lead gets one surgical confirming request. These turn a scanner line into evidence
without over-reaching — redact before storing, and stop at proof-of-presence.

```bash
# git_dir / svn_dir — prove a live repo, not a 200 catch-all
curl -s https://example.com/.git/HEAD            # ref: refs/heads/main
curl -s https://example.com/.git/config          # [core] / [remote "origin"]
curl -s https://example.com/.svn/wc.db -o /dev/null -w '%{http_code} %{size_download}\n'

# dotenv / rails_database_yml / symfony_databases_yml / magento_config — confirm live keys
curl -s https://example.com/.env | grep -iE 'APP_KEY|DB_|SECRET|PASSWORD'     # then redact
curl -s https://example.com/config/database.yml | head

# privatekey / sshkey — confirm it is an actual key, capture only the header line
curl -s https://example.com/server.key | head -1     # -----BEGIN (RSA |EC |OPENSSH) PRIVATE KEY-----

# sql_dump / backup_archive — confirm type/size without downloading the whole dump
curl -sI https://example.com/backup.sql            # Content-Type / Content-Length
curl -s https://example.com/dump.sql | head -c 200  # CREATE TABLE / INSERT INTO ...

# adminer / telescope / elmah — confirm the panel identity; do NOT authenticate
curl -s https://example.com/adminer.php | grep -i 'Adminer'
curl -s https://example.com/telescope/requests -o /dev/null -w '%{http_code}\n'

# openelasticsearch — confirm the banner only; do NOT query indices
curl -s https://example.com:9200/ | grep -i '"tagline"'   # "You Know, for Search"

# optionsbleed (CVE-2017-9798) — a malformed Allow header leaking memory
curl -sI -X OPTIONS https://example.com/ | grep -i '^Allow:'   # garbage/overlong = leak

# citrix_rce (CVE-2019-19781) — traversal reachability (presence check, not exploitation)
curl -s --path-as-is https://example.com/vpn/../vpns/ -o /dev/null -w '%{http_code}\n'
```

If the artefact does not confirm under its own request — a `.git/config` that returns the site's
200 marketing page, an `.env` that is empty — it was a soft-404 false positive. Drop it; do not
report it.

---

## Reporting evidence

Capture, per finding:

- **The snallygaster line itself** — the test name plus the exact URL, or the `-j` JSON record.
  Name the test (e.g. `dotenv`, `git_dir`) and link its entry in upstream `TESTS.md` so the
  triager sees the issue class and references in one click.
- **The exact invocation** — the full command including any `-t` / `--nowww` / `--useragent`
  flags, and a timestamp, so the test is reproducible and demonstrably single-pass / low-volume.
- **The verifying request/response pair** — the Phase 4 `curl -i` and its response proving the
  artefact is real and sensitive: `/.git/HEAD` + `/.git/config` for a repo; the `[core]`/
  `[remote]` section, not just a 200; a *redacted* `.env` line showing a live key exists
  (`DB_PASSWORD=****…**ab`, first/last chars only); the Adminer/Elasticsearch banner.
- **The soft-404 baseline** — the calibration request from Phase 1 (a random path returning
  `404`), pre-empting the "this is a false positive" rejection.
- **Impact framed by contents, not by the test name.** State what the file actually exposes and
  the realistic consequence (e.g. "the `.env` contains the production database password and
  Laravel `APP_KEY`, enabling DB access and session forgery"). An empty or placeholder file is
  Info; map severity to the program's rubric from there.
- **Minimal data handling.** Never exfiltrate more than the proof needs, redact every secret
  before it leaves your notes, and say in the report that you did so and did not authenticate /
  did not clone the full repository.

---

## References

- snallygaster repository and README (install, dependencies, overview) — https://github.com/hannob/snallygaster
- `TESTS.md` — authoritative list and descriptions of every default/info test, with the issue
  references (incl. CVE-2017-9841 PHPUnit, CVE-2019-19781 Citrix, CVE-2017-9798 Optionsbleed) —
  https://github.com/hannob/snallygaster/blob/master/TESTS.md
- Source `snallygaster` script — the argparse definitions grounding every flag above
  (`-t/--tests`, `--useragent`, `--nowww`, `--nohttp`, `--nohttps`, `-i/--info`, `-n/--noisy`,
  `-p/--path`, `-j/--json`, `-d/--debug`; no `--version`/`--timeout`) —
  https://raw.githubusercontent.com/hannob/snallygaster/master/snallygaster
- PyPI package (`pip3 install snallygaster`) — https://pypi.org/project/snallygaster/

*Research supplement added 2026-11-16, grounded in the snallygaster README, TESTS.md, and source argparse at https://github.com/hannob/snallygaster (model: claude-opus-4-8).*
