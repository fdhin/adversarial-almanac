---
name: "recon-ng"
categories:
  - blackarch-recon
version: "v5.1.2.r8.g32b08fe"
homepage: "https://github.com/lanmaster53/recon-ng"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "lanmaster53/recon-ng"
github-stars: 6039
github-pushed: "2026-10-05"
github-archived: false
github-issues: 40
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# recon-ng

> Modular, Metasploit-style framework for web-based open-source reconnaissance, with a module marketplace and a per-workspace SQLite database.

## What it's for

Recon-ng is a full-featured reconnaissance framework with a console look-and-feel modeled on the Metasploit Framework. Everything is driven from an interactive shell: a module marketplace (the framework ships no modules — you install them from the marketplace), workspaces that isolate per-engagement databases and configuration under `~/.recon-ng/workspaces/`, and a relational database where each harvested record becomes "seed" input for further modules. It is designed exclusively for open-source/web-based reconnaissance, not exploitation or active network scanning. Findings live in the workspace database and are queried with SQL or exported by reporting modules / the bundled `recon-web` interface, and the whole console is scriptable via resource files or the `recon-cli` command-line client.

## Typical use cases

### Start a session in a dedicated workspace

```
recon-ng -w client-a
```

Outcome: opens the interactive console with the `client-a` workspace created/loaded — a separate SQLite database, config and loot folder — so this engagement's data never mixes with other projects. Inside, `workspaces list`/`workspaces load` switch contexts.

### Find and install modules from the marketplace

```
marketplace search domains-
marketplace install recon/domains-hosts/hackertarget
```

Outcome: the search lists every module whose input table is `domains` (the module tree path is `recon/<input>-<output>/<module>`, so `search -hosts` shows everything that produces hosts); the install downloads the module into the workspace. Run `marketplace info <module>` first — asterisks in the `D`/`K` columns flag dependency and API-key requirements that must be met manually.

### Load a module and run it against a target

```
modules load hackertarget
options set SOURCE example.com
run
```

Outcome: loads the module (smart loading resolves the unique keyword to `recon/domains-hosts/hackertarget`), points its `SOURCE` at the domain — the option also accepts a file path or SQL query — and executes, storing every discovered host in the workspace database instead of just printing it.

### Query the harvested results

```
db query select host, ip_address, source from hosts;
```

Outcome: runs SQL against the workspace database and prints the stored rows; `db schema` renders the live table layout to help build queries, and `db insert` seeds records manually.

### Replay a whole methodology from a resource file

```
recon-ng -r recon.rc
```

Outcome: feeds every command in `recon.rc` to the interpreter in sequence (record one interactively with `script record <file>`), then returns the prompt (`recon-ng > EOF`) — the standard way to re-run an identical recon pipeline across engagements; `recon-cli` exposes the same functionality for shell scripting.

## Bug bounty workflow with recon-ng

Recon-ng does not find vulnerabilities. It is an **input engine**: it harvests assets, people, and exposure from public sources into a queryable SQLite database, and that database becomes the seed list every *other* tool (takeover checkers, nuclei, your BOLA scripts) runs against. On a bug bounty program its value is scope expansion done quietly and auditably — every row carries the module that produced it, so you can always show a triager where a host came from. Work it in phases, passive-first, and keep the active modules gated behind the program's rules of engagement.

Recon-ng ships **zero modules** — the framework is just the console and the database; everything is pulled from the marketplace (`~/.recon-ng/modules/` once installed). Workspaces isolate engagements under `~/.recon-ng/workspaces/<name>/`, but **API keys live in a separate `~/.recon-ng/keys.db`** and are shared across every workspace, so you add a key once and every engagement sees it. Keep that in mind for scope hygiene: the same Shodan key will happily query a host that is out of scope for the program you are in.

### Phase 0 — Workspace, scope boundary, and telemetry off

```
recon-ng -w acme --stealth
```

`--stealth` sets all three `--no-*` flags at once (`--no-version`, `--no-analytics`, `--no-marketplace`) — it suppresses **recon-ng's own outbound calls** (the version check, the analytics beacon, and marketplace index sync), not your module traffic. It does not make modules quiet; it stops the framework phoning home. Use it on engagements where you do not want recon-ng itself reaching Anthropic/GitHub endpoints mid-assessment. Refresh the marketplace once *before* going stealth (`marketplace refresh`) so your index is current.

Define scope before you seed anything. Recon-ng has no scope-enforcement feature — the discipline is yours. Keep the program's in-scope domains/netblocks in a file and only ever `set SOURCE` to those values or a SQL query that filters to them. One workspace per program keeps data (and later the export) from bleeding across engagements.

### Phase 1 — Curate a passive-first module set

Do not `marketplace install all` — it pulls dozens of modules, many of which touch the target or need keys, and it buries the ones you want. Install deliberately:

```
marketplace search domains-hosts
marketplace info recon/domains-hosts/certificate_transparency
marketplace install recon/domains-hosts/certificate_transparency
```

`marketplace info` prints the `D` and `K` columns as asterisks: **`D` = unmet dependencies** (a Python package you must `pip install` yourself) and **`K` = a required API key**. A module installed with an unmet dependency loads as `disabled`; one missing its key loads but warns and usually fails at run time. Resolve both before you build a methodology around a module.

The passive core for a program that starts from a domain:

| Path | What it queries | Touches target? |
|---|---|---|
| `recon/domains-hosts/certificate_transparency` | crt.sh CT logs | No (third-party) |
| `recon/domains-hosts/hackertarget` | hackertarget.com API | No |
| `recon/domains-hosts/threatcrowd`, `.../threatminer` | threat-intel passive DNS | No |
| `recon/domains-hosts/bing_domain_api` | Bing Search API (needs `bing_api`) | No |
| `recon/domains-hosts/ssl_san` | SAN fields of the cert | one TLS connection |
| `recon/hosts-hosts/resolve`, `.../reverse_resolve` | DNS via `NAMESERVER` | DNS lookups |
| `recon/hosts-ports/shodan_ip` | Shodan (needs `shodan_api`) | No (Shodan did the scanning) |

### Phase 2 — Seed domains, expand to hosts (passive)

Seed the root domain into the `domains` table, then run the domains→hosts modules. Each discovered host is stored, not just printed, so it becomes input for the next stage:

```
db insert domains example.com~
modules load certificate_transparency
options set SOURCE query SELECT domain FROM domains
run
```

Setting `SOURCE` to a SQL query (rather than a single string) chains the whole table through the module — add a `WHERE` clause to stay in scope when a workspace holds more than one root. `certificate_transparency` is the single highest-yield passive subdomain source; follow it with `hackertarget`, `threatcrowd`, and `bing_domain_api` to triangulate. Dedup happens in the DB automatically (host is unique per workspace).

### Phase 3 — Enrich hosts: resolution and exposure

```
modules load resolve
options set SOURCE query SELECT DISTINCT host FROM hosts WHERE ip_address IS NULL
run
modules load shodan_ip
options set SOURCE query SELECT DISTINCT ip_address FROM hosts WHERE ip_address IS NOT NULL
run
```

`resolve` fills `ip_address` on every host (DNS against `NAMESERVER`, default `8.8.8.8`); `shodan_ip` enriches each IP with open ports and banners **from Shodan's existing scan data** — you never touch the target, which is exactly why it is bounty-safe. This is where forgotten hosts, stale services, and exposed panels surface as candidates for your active tooling.

### Phase 4 — People, repositories, and breach exposure

```
modules load recon/domains-contacts/whois_pocs
options set SOURCE example.com
run
modules load recon/contacts-credentials/hibp_breach
options set SOURCE query SELECT email FROM contacts WHERE email IS NOT NULL
run
```

Contacts and breach data (`hibp_breach`, `hibp_paste`) build the human surface. Be careful here: on most bug bounty programs, employee emails and breach hits are **context, not a finding** — using leaked credentials to log in is usually explicitly out of scope and can get you banned. Harvest it for the report's impact narrative, do not weaponize it without written permission. `recon/profiles-repositories/*` and the GitHub modules surface leaked source and secrets, which *is* often reportable.

### Phase 5 — Optional active modules (RoE-gated)

A handful of modules cross from passive OSINT into **touching the target directly** — treat them as active scanning and only run them if the program permits it:

- `recon/domains-hosts/brute_hosts` — DNS-brute-forces subdomains against `NAMESERVER`. Generates a burst of queries and many NXDOMAINs. Cap it: `options set THREADS 2` and prefer CT-log results first.
- `discovery/info_disclosure/interesting_files` — makes **HTTP requests to the target web server** for `robots.txt`, `sitemap.xml`, `phpinfo.php`, backup/archive names. This is active content discovery; it hits the target, so it belongs under the program's web-scanning rules.
- `discovery/info_disclosure/cache_snoop` — DNS cache-snooping against a specified resolver.

### Phase 6 — Query, triage, export, hand off

```
db query SELECT host, ip_address, module FROM hosts WHERE host LIKE '%.example.com' ORDER BY host
dashboard
```

The `module` column on every table is your provenance trail — it records which module inserted each row. Export the curated host list and feed it to the tools that actually test (takeover checkers, httpx, nuclei, your BOLA scripts). Recon-ng's job ends at a clean, sourced asset inventory.

## Where the money is with recon-ng

Recon-ng is a feeder, so "the money" is the finding class each harvest *enables* downstream — the severity and dupe risk below are for the eventual finding, not for recon-ng output itself.

| Priority | Finding class recon-ng feeds | Module(s) that reach it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Subdomain takeover candidate | `domains-hosts/certificate_transparency`, `hackertarget`, `bing_domain_api` → resolve → takeover check | High | Med–High |
| 2 | Forgotten host / exposed panel / stale service | `hosts-ports/shodan_ip`, `hosts-hosts/resolve`, `netblocks-hosts/*` | High | Medium |
| 3 | Exposed sensitive files (config/backup/debug) | `discovery/info_disclosure/interesting_files` (active) | Med–High | Medium |
| 4 | Asset-scope expansion (new in-scope hosts/domains) | `domains-domains`, `hosts-domains`, `certificate_transparency` | Enabler | Low if fresh |
| 5 | Leaked source / secrets in repos | `profiles-repositories/*`, `repositories-vulnerabilities/*`, GitHub modules | High | Medium |
| 6 | Tech-stack / version intel (CVE matching) | `domains-hosts/builtwith`, `netcraft`, `hosts-hosts/virustotal` | Info → enabler | — |
| 7 | Breach / credential exposure (context only) | `contacts-credentials/hibp_breach`, `hibp_paste` | Low / often OOS | High |
| 8 | Employee/email surface (phishing — usually OOS) | `domains-contacts/whois_pocs`, `hunter_io`, `pgp_search` | Low / OOS | High |

Freshly-shipped subdomains (priority 1/4) are the least-duplicated payouts: CT logs surface a host the moment its cert is issued, often before anyone else has reviewed it. Diff successive `certificate_transparency` runs across days — new rows are the newest, least-tested attack surface on the program.

## Command recipes

Low-volume settings are the defaults for everything that touches the target: `THREADS` low, `TIMEOUT` generous, passive sources first, active modules last and only under RoE.

### Build a passive module set

```
marketplace refresh
marketplace search certificate
# -> table: path | version | last_updated | D | K | status
marketplace info recon/domains-hosts/certificate_transparency
marketplace install recon/domains-hosts/certificate_transparency
```

Expected: `install` reports the module copied; `search`/`info` show `D`/`K` asterisks. A `K` asterisk means add the key (next recipe) before the module will return data.

### Add and verify API keys (shared across workspaces)

```
keys add shodan_api AV3xxxxxxxxxxxxxxxxxxxxxxxx
keys list
# -> Name | Value  (stored in ~/.recon-ng/keys.db, not the workspace DB)
```

Key names are exact and module-specific: `shodan_api`, `bing_api`, `github_api`, `google_api` + `google_cse`, `censysio_id` + `censysio_secret`, `hashes_api`, `builtwith_api`, `fullcontact_api`, `virustotal_api`, `ipinfodb_api`. `keys remove <name>` deletes one. Because keys persist across workspaces, confirm you are *authorized* to query a given source against this program before running a keyed module.

### Run a module non-interactively (scriptable)

```
recon-cli -w acme -m recon/domains-hosts/hackertarget -o SOURCE=example.com -x
```

`recon-cli` flags differ from the console: `-m` picks the module, `-o name=value` sets a module option (repeatable), `-c` runs a module-context command before the run, `-C` runs a global-context command, `-g name=value` sets a global option, `-G`/`-M`/`-O` list global options / modules / module options, and `-x` actually runs it. Omit `-x` and it loads but does not execute — handy for `-O` to inspect options. Lower throughput globally with `-g THREADS=2 -g TIMEOUT=20`.

### Cap the active modules

```
modules load brute_hosts
options set SOURCE example.com
options set THREADS 2
run
```

`brute_hosts` and `interesting_files` are the two modules that generate real traffic; `THREADS 2` keeps the DNS/HTTP burst low enough not to trip rate limits or look like an attack. Never raise `THREADS` on these for a bounty target.

### Export evidence

```
marketplace install reporting/csv reporting/list reporting/json
modules load reporting/csv
options set TABLE hosts
options set FILENAME ~/engagements/acme/hosts.csv
run
```

Reporting modules (`reporting/csv`, `list`, `json`, `xml`, `xlsx`, `html`, plus `pushpin`/`proxifier`) read a `TABLE`/`COLUMNS` selection out of the workspace DB and write a file. `reporting/list` emits a flat newline-delimited column — ideal for piping a host list straight into `httpx` or `nuclei`.

### Capture a reproducible run

```
spool start ~/engagements/acme/recon.log
script record acme.rc
...run your module chain...
script stop
spool stop
dashboard
```

`spool` tees all console output to a log (evidence); `script record` captures the exact commands into a resource file you can replay on the next engagement with `recon-ng -w acme2 -r acme.rc`; `dashboard` prints an **Activity Summary** (Module / Runs) and **Results Summary** (Category / Quantity) — a one-screen audit of what you ran and what it produced.

## Pitfalls, noise and scope discipline

**Recon-ng enforces no scope — you do.** `SOURCE` will accept any domain you type and any SQL query you write, including rows that belong to another program in the same workspace. The guardrails are convention: one workspace per program, and `SOURCE` values that are either an explicit in-scope string or a `WHERE`-filtered query. A keyed module (Shodan, Censys, VirusTotal) querying an out-of-scope asset is a scope violation even though recon-ng only talked to a third party — the lookup is still *about* a system you were not authorized to recon.

**Know which modules touch the target.** The overwhelming majority of modules only query third-party APIs and search engines — genuinely passive, invisible to the target. The exceptions that cross the line and must be RoE-gated: `brute_hosts` (DNS flood), `discovery/info_disclosure/interesting_files` (HTTP requests straight to the target), `cache_snoop` (resolver snooping), and `resolve`/`reverse_resolve`/`mx_spf_ip` (DNS lookups of target names, lightweight but not zero). Running `interesting_files` against a target under a program that forbids active scanning is how a "recon-only" session becomes a RoE breach.

**Search-engine scrapers get *your* IP banned, not the target's.** `google_site_web`, `bing_domain_web`, and `metacrawler` scrape result pages; Google/Bing throttle and CAPTCHA aggressively, so these fail quietly or lock your source IP out of search for a while. Prefer the API-keyed equivalents (`bing_domain_api`) where a program's intensity is a concern.

**`install all` is a trap.** It drags in modules with unmet dependencies (they load `disabled` and clutter `modules search`), keyed modules you have no key for (they warn on every load), and the active modules above (which you may run by accident). Install the handful you planned in Phase 1.

**Stale data and dormant upstream.** The framework sees roughly one commit a year; several marketplace modules point at APIs that have changed auth, gone paid, or shut down, so a module can return nothing not because the target is clean but because the source is dead. Corroborate a critical absence with a second module or a direct check — never report "no subdomains found" on the strength of one module returning empty. The `K`/`D` asterisks and a quick `keys list` tell you whether an empty result is "no data" or "misconfigured".

**Breach data is not a login license.** `hibp_breach`/`hibp_paste` output is for impact context. Credential stuffing or logging in with a harvested password is out of scope on almost every program and is the fastest route to a ban — keep it in the report narrative, not in a request.

## Reporting evidence

Recon-ng's database *is* the evidence, and its best feature for a report is per-row provenance. Capture:

- **The sourced asset table.** `db query SELECT host, ip_address, module FROM hosts WHERE host LIKE '%.example.com'` — the `module` column proves each host came from a public source (CT logs, Shodan), which pre-empts the "how did you find this" question and demonstrates the finding rests on open-source data, not intrusive scanning.
- **A reporting-module export** (`reporting/csv` or `reporting/json`) attached as the raw inventory, plus `reporting/list` output as the clean host list you fed to downstream tooling — so the triager can reproduce your pivot.
- **The `spool` log** of the exact console session (commands + output) and the **`script record` resource file**, together giving a replayable, timestamped account of what you ran. Reproducibility is what separates a credible recon chain from "trust me."
- **The `dashboard` summary** screenshot/paste — Activity Summary (which modules, how many runs) and Results Summary (counts per table) — as a one-glance scope statement showing the breadth and the restraint of the session.
- **For a specific finding** (e.g. an exposed file via `interesting_files` or a takeover candidate from `certificate_transparency` → resolve), the single `db query` row plus the module name, then the request/response pair from the *downstream* tool that confirmed it. Recon-ng establishes the candidate; the confirmation evidence comes from the validating tool, and the report should show both links in the chain.

Keep exports inside the engagement directory, redact anything beyond what the proof needs (especially contact/credential tables), and delete the workspace when the engagement closes — `workspaces remove acme` drops the database and its loot.

## recon-ng vs. bbot — mature framework vs. modern pipeline

Both are OSINT engines; they are built on opposite philosophies, and the right pick depends on whether you want control or coverage.

| | recon-ng | bbot |
|---|---|---|
| Model | Interactive console, **manual** module chaining | **Event-driven, recursive** auto-chaining |
| Data store | Relational SQLite per workspace, SQL-queryable | Event graph / scan output, NDJSON |
| Execution | You load and run each module | Modules fire automatically as events emerge |
| Throughput | Deliberate, low-volume | High, async, broad |
| Cadence | Mature, slow (≈1 commit/yr) | Modern, actively developed |
| Best for | Auditable, surgical, evidence-first recon | Fast, wide, hands-off asset discovery |

The practical split: **bbot sprays and recurses** — point it at a domain and it auto-pivots domain→subdomain→IP→port→tech→more subdomains, which is unbeatable for breadth and speed but noisy and harder to attribute row-by-row. **recon-ng is surgical** — you choose exactly which sources fire, every result is a SQL row tagged with its module, and nothing happens you did not type. On a bug bounty program with strict RoE and a triager who will ask where each host came from, recon-ng's auditability and passive-by-default control are the advantage; when you just need the widest possible asset list fast on a broad-scope program, bbot covers more ground. Many hunters run both: bbot for the initial sweep, recon-ng to enrich and document the assets that matter.

## References

- recon-ng repository (lanmaster53/recon-ng) — https://github.com/lanmaster53/recon-ng
- recon-ng wiki: Getting Started — https://github.com/lanmaster53/recon-ng/wiki/Getting-Started
- recon-ng wiki: Features — https://github.com/lanmaster53/recon-ng/wiki/Features
- recon-ng wiki: API Keys — https://github.com/lanmaster53/recon-ng/wiki/API-Keys
- recon-ng-marketplace (official module repo) — https://github.com/lanmaster53/recon-ng-marketplace
- marketplace wiki: API Keys (key names) — https://github.com/lanmaster53/recon-ng-marketplace/wiki/API-Keys
- `recon-ng` / `recon-cli` / `recon-web` launcher argument definitions (master branch source) — https://raw.githubusercontent.com/lanmaster53/recon-ng/master/recon-ng
- Core console commands (`do_*`/`_do_*` in framework.py and base.py) — https://raw.githubusercontent.com/lanmaster53/recon-ng/master/recon/core/base.py
- Kali Tools: recon-ng (v5.1.2) — https://www.kali.org/tools/recon-ng/
- bbot (comparison) — https://github.com/blacklanternsecurity/bbot

*Research supplement added 2026-11-16, grounded in the recon-ng master-branch launcher and core sources (recon-ng/recon-cli/framework.py/base.py), the recon-ng and recon-ng-marketplace wikis, and the Kali Tools page (URLs above); flags and commands verified against current source, not memory (model: claude-opus-4-8).*
