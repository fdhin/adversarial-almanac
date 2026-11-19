---
name: "theharvester"
categories:
  - blackarch-recon
version: "4.11.1.r76.g760b386"
homepage: "https://github.com/laramies/theHarvester"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-08-02"
build-system: python
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "laramies/theHarvester"
github-stars: 18250
github-pushed: "2026-11-06"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 778
gh-activity-checked: "2026-11-09"
---

# theharvester

> Aggregate emails, subdomains, hosts, IPs, ASNs, URLs, people and breach names for a domain from 60+ public OSINT sources.

## What it's for

theHarvester (by Christian Martorella, @laramies) gathers open-source intelligence about a domain or organization from search engines, certificate transparency logs, DNS datasets, code repositories and threat-intelligence platforms, then normalizes the findings. Sources are selected by name (`crtsh`, `certspotter`, `dnsdumpster`, `shodan`, ...) or by capability (`emails`, `subdomains`, `ips`, `asns`, `urls`, `people`, `breaches`), and each run reports per-source outcomes — a source can return zero findings or fail without erasing what other sources retained. It classifies activity by network behavior: P0 passive sources never touch the target, while P1 (DNS) and P2 (direct) sources run only when explicitly selected. It does not attack or scan targets; it collects and correlates public data and writes JSONL/JSON/XML reports plus a local SQLite evidence store.

## Typical use cases

### First passive run with no API keys

Harvest emails and hostnames using two free certificate-transparency sources.

```
theHarvester -d example.com -b crtsh,certspotter
```

Outcome: both P0 passive sources run (the target itself is not contacted); the terminal reports each source's outcome and prints deduplicated emails and hostnames found for example.com.

### Select sources by result type

Ask for specific evidence classes instead of naming sources.

```
theHarvester -d example.com -b emails,ips,urls
theHarvester -d example.com -b subdomains
```

Outcome: capability selectors form a union and choose which sources run — every cataloged P0 passive source that can yield emails, IPs or URLs is queried, with other result types those sources happen to return kept as well. `-b all` runs every P0 source.

### Use DNS and Shodan sources that need API keys

Add key-gated discovery sources for subdomains and IP data.

```
theHarvester -d example.com -b dnsdumpster,shodanInternetDB
theHarvester -d example.com -b shodan
```

Outcome: with keys configured in `api-keys.yaml`, dnsdumpster returns subdomains and IPs, shodan (Shodan Host REST API key required) contributes subdomains, and shodanInternetDB works without any key; the run summary shows each source as completed, partial or failed.

### Save a machine-readable report

Write findings for later diffing or pipeline consumption.

```
theHarvester -d example.com -b crtsh,certspotter -f report
```

Outcome: writes `report.jsonl` (one sorted, deduplicated finding per line with source provenance — the primary automation format) plus `report.json` and `report.xml`, and retains the completed run in the local SQLite evidence store (`~/.local/share/theHarvester/stash.sqlite`).

### Resolve the hosts that were discovered

Turn harvested hostnames into resolved addresses for an authorized domain.

```
theHarvester -d example.com -b crtsh,certspotter -r
```

Outcome: after the passive phase, discovered hosts are resolved with the default resolver list; expect `ip` findings in the output alongside hostnames.

### Cap result volume and compare runs over time

Bound a chatty source and review stored evidence afterwards.

```
theHarvester -d example.com -b jsmon -l 100
harvest-report hostname-changes --target example.test
```

Outcome: `-l 100` caps pages/results collected per source (`-l 0` follows everything), and `harvest-report hostname-changes` replays stashed runs to show which hostnames appeared or disappeared between them (`--include-still-reported` keeps stable ones).

## Bug bounty workflow with theHarvester

theHarvester is a **surface-mapping** tool, not an exploit tool: it tells you *what belongs to the target* before you touch anything. On a bug bounty program it earns its place in Phase 0 — the moment after you read the scope and before you point a single active scanner at an asset. Treat the whole run as passive-first OSINT; the active phases (`-c`, `-n`, `-r`, `-t`, `-a`, `--vhost`, `--screenshot`, `-s`) are opt-in and you enable them one at a time, only against in-scope names, and only when the program permits active testing.

**The hard line this tool sits on:** theHarvester collects emails, people names, and breach references. That is in-scope OSINT — passive collection of already-public data. The moment you *contact* any of those people, send a phish, or try a harvested credential against a login, you have crossed from reconnaissance into social engineering, which virtually every program forbids and which is out of scope by default. Harvested emails are for understanding the organization's naming convention and mail infrastructure, nothing else. Keep that line bright: **collect, correlate, never contact.**

### Phase 0 — Scope intake and dry run

Confirm which apex domains, subdomain wildcards, ASNs, and IP ranges the program authorizes. theHarvester's `-d` takes a company name or domain (and, for `--routeviews`, an explicit ASN/IP/CIDR). Run one cheap passive source first to confirm the tool is configured and the domain resolves to a real footprint before committing to a wider run:

```
theHarvester -d example.com -b crtsh
```

This touches only crt.sh (a Certificate Transparency aggregator), never example.com itself. If the summary line reports `crtsh` as completed with a plausible count, your pipeline works.

### Phase 1 — Passive-only harvest (P0 sources)

Collect the no-key, non-target-contacting sources first. These query third-party datasets and search engines; the target's own servers are never hit. Select by capability to cast a wide union, or name specific sources for reproducibility:

```
theHarvester -d example.com -b subdomains -f example-passive
theHarvester -d example.com -b crtsh,certspotter,otx,rapiddns,subdomaincenter,urlscan,waybackarchive -f example-passive
```

`-b subdomains` runs every cataloged source that can yield subdomains; `-b all` runs **every P0 source only** (P1/P2 are never implied by `all` — they require explicit selection). `-f` writes `example-passive.jsonl` / `.json` / `.xml`. The JSONL is your pipeline input (Phase 3).

### Phase 2 — Keyed source enrichment (still passive)

If you have provider API keys configured in `api-keys.yaml`, add the high-signal keyed sources. These are still passive toward the target (they query the provider, not example.com), but they return assets the free sources miss:

```
theHarvester -d example.com -b securityTrails,virustotal,censys,netlas,fullhunt,shodan -f example-keyed
```

`shodan` here is the Shodan *source* (subdomains, P1 — needs a key). Do not confuse it with the `-s/--shodan` *action*, which queries the Shodan Host API for IPs you already discovered. `shodanInternetDB` and `subdomainfinderc99` are keyless but classed P1 — select them deliberately.

### Phase 3 — Normalize and hand off to the resolver pipeline

theHarvester deliberately keeps DNS resolution and brute force *out* of the passive harvest. The idiomatic pipeline is: harvest passively here, then hand the hostname list to a dedicated resolver/brute-force stage (amass, subfinder, or theHarvester's own P1 phases). Extract clean hostnames from the JSONL:

```
jq -r 'select(.type=="host") | .value' example-passive.jsonl | sort -u > hosts.txt
amass enum -passive -df <(echo example.com) -nf hosts.txt   # merge, dedupe, enrich
subfinder -dL <(echo example.com) | anew hosts.txt          # union with subfinder
```

Now resolve and probe the *union*, not theHarvester's output alone — the value of theHarvester is breadth of sources, and its output is strongest when it widens a corpus the other tools also feed.

### Phase 4 — Opt-in active phases (only if the program allows active testing)

These contact DNS infrastructure or the hosts directly. Enable one at a time, read the output, then decide on the next. Each is scoped to in-scope names:

```
theHarvester -d example.com -b crtsh,certspotter -r                 # P1: resolve harvested hosts
theHarvester -d example.com -b crtsh,certspotter -c                 # P1: DNS brute force
theHarvester -d example.com -b crtsh,certspotter -t -f example-takeover   # P2: takeover indicators
```

`-t/--take-over` flags *provider-gated takeover indicators* — dangling CNAMEs pointing at unclaimed cloud resources. These are **indicators, not confirmed takeovers**; verify manually before reporting (Phase 5). `--screenshot DIR` and `-a/--api-scan` send direct browser/HTTP requests and are the most intrusive — save them for explicitly active-authorized programs.

### Phase 5 — Validate the lead, then switch tools

theHarvester hands you *candidates*, never findings. A subdomain-takeover indicator must be confirmed by actually observing the unclaimed resource; a "forgotten" host must be confirmed live and in-scope; a historical URL from `waybackarchive` must be re-requested to see if the endpoint still exists. The finding is written against the confirmed asset, with theHarvester cited only as how you *discovered* it.

---

## Where the money is with theHarvester

theHarvester does not find vulnerabilities — it finds the assets on which other tools find vulnerabilities. The payouts come from what its breadth surfaces that a single CT-log lookup would miss. Dupe risk is structurally **high** for raw output (every hunter runs crtsh on day one); the edge is correlation and the long-tail sources.

| Finding class | Feature / source that reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| Subdomain takeover | `-t` indicators on hosts from `crtsh`/`certspotter`/`subdomaincenter`, confirmed manually | High | Medium — indicator is a lead, the confirmed takeover is yours |
| Forgotten / dev / staging host in scope | broad `-b subdomains` union, esp. `rapiddns`, `subdomaincenter`, `subdomainfinderc99`, `otx` | Medium–High | High on common names, lower on long-tail sources |
| Exposed service on a harvested IP | `-s/--shodan` on discovered IPs; `shodanInternetDB` source | Medium–High | Medium |
| Historical endpoint / leaked parameter | `waybackarchive`, `urlscan`, `commoncrawl` → re-request live | Medium | Medium |
| Scope expansion via ASN/CIDR | `--routeviews` ASN attribution on discovered IPs | Info → enabler | Low (few hunters pivot to ASNs) |
| Code/secret references | `github-code` (keyed), `gitlab`, `sourcegraph` | High if a live secret | Medium |
| Credential-exposure context | `haveibeenpwned`, `hudsonrock`, `darkeye` (breach names only) | Info / context | N/A — never a standalone finding |
| Org naming-convention map | `emails`, `people` capabilities | Info / enabler | N/A |

Two framing rules keep these honest in a report: **(1)** email and people results are *context for scope mapping*, not findings and never a basis for contact; **(2)** theHarvester's provenance (`sources`/`actions` in the JSONL) is what makes a "forgotten asset" report credible — it shows exactly which public source exposed the asset.

---

## Command recipes

Annotated runs, with the low-volume settings called out. Defaults: `-l/--limit` is **500** results per source (`-l 0` removes the cap and follows providers to exhaustion — loud and slow, avoid on first contact); `-j/--source-workers` is **3** concurrent sources; `-S/--start` is the pagination offset (default 0).

### Minimal passive footprint

```
theHarvester -d example.com -b crtsh,certspotter -l 100
```
Expected: two P0 sources run; the terminal prints each source's outcome and a deduplicated host/email list. `-l 100` keeps each source to its first 100 results — plenty for a first look and gentle on free-tier providers.

### Full passive sweep to a report file

```
theHarvester -d example.com -b all -f example-recon
```
Expected: every **P0** source runs (no P1/P2), writing `example-recon.jsonl` (summary line + one finding per line), `example-recon.json`, and `example-recon.xml`. The run is retained in `~/.local/share/theHarvester/stash.sqlite`. `-b all` is passive — it will not resolve, brute, or contact the target.

### Keep provider load low on a chatty source

```
theHarvester -d example.com -b rapiddns -l 200 -j 1
```
Expected: `-j 1` serializes sources (one at a time) and `-l 200` caps volume — the polite setting when a provider is rate-sensitive or you want deterministic, low-concurrency behavior. theHarvester already stops a source on HTTP 403/429 without retry and keeps earlier pages, but capping volume yourself avoids tripping the limit at all.

### Resolve discovered hosts with your own resolvers (P1, active)

```
theHarvester -d example.com -b crtsh,certspotter -r 198.51.100.53
theHarvester -d example.com -b crtsh,certspotter -r ./resolvers.txt
```
Expected: after the passive phase, harvested hostnames are resolved; host values can take the `hostname:IP` form. `-r` takes comma-separated resolver IPs or a one-IP-per-line file, or omit the value for defaults. Use `--dns-resolvers` to *select* resolvers for other DNS actions without turning on resolution. The resolve phase uses at most 20 hostname jobs.

### Bound recursive DNS discovery

```
theHarvester -d example.com -b crtsh -r --dns-recursive-depth 1 \
  --dns-recursive-query-limit 500 --dns-recursive-runtime-seconds 60
```
Expected: recursion is **off by default** (`--dns-recursive-depth 0`). When you enable it, the two hard caps (`--dns-recursive-query-limit`, `--dns-recursive-runtime-seconds`) bound the blast radius so a recursive walk cannot run away. Keep depth at 1 on a bounty unless you have a reason to go deeper.

### Enrich IPs without touching the target further

```
theHarvester -d example.com -b crtsh,certspotter,shodanInternetDB -s --routeviews -f example-enriched
```
Expected: `-s/--shodan` queries the Shodan Host API for discovered IPs (needs a Shodan key); `--routeviews` attributes ASN/prefix via RouteViews. Both enrich *already-discovered* IPs from third-party datasets — no new packets to example.com. Note: RouteViews routing relationships **do not establish ownership or scope** — confirm the CIDR is in the program before probing it.

### Subdomain-takeover indicator pass (P2, active HTTP)

```
theHarvester -d example.com -b crtsh,certspotter,subdomaincenter -t -f example-takeover
```
Expected: takeover indicators for dangling records. `-t` uses the configured resolvers and wildcard controls, does not follow redirects, and routes HTTP confirmation through the proxy if `-p` is set. Output is indicators, not confirmations.

### Run everything through a proxy (fail-closed)

```
theHarvester -d example.com -b crtsh,certspotter -p
```
Expected: supported HTTP(S) requests use `proxies.yaml`; if no proxy is configured the run **fails immediately** rather than leaking your real IP. DNS queries still use the selected resolvers outside the proxy. Use this when the program requires traffic from a declared source IP.

### Review what changed between runs

```
theHarvester -d example.com -b crtsh,certspotter -f run1      # later, after re-running:
harvest-report hostname-changes --target example.com
harvest-report hostname-changes --target example.com --include-still-reported
harvest-report targets
harvest-report contributions --all-targets --format json
```
Expected: `hostname-changes` replays stashed runs and buckets hosts into `newly_reported`, `still_reported`, `no_longer_reported`, and `uncertain` (`still_reported` is hidden unless `--include-still-reported`). It takes `--target` *or* `--run-id`, not an arbitrary pair of run IDs. `targets` lists stored targets; `contributions` shows per-source yield (`--all-targets` for the whole stash). A missing `--database` is an error — the subcommands never create one.

---

## Pitfalls, noise and scope discipline

**Contacting harvested people = instant scope violation.** The single fastest way to get banned from a program with this tool is to treat harvested emails or names as targets for outreach. Passive collection is in scope on most programs; social engineering is out of scope on nearly all. Never email, call, DM, or phish anyone theHarvester surfaces, and never feed harvested emails into a credential-stuffing or password-spray run. If the program's policy is silent on OSINT, assume collection-only and ask.

**`all` and `-l 0` are not the same kind of "more".** `-b all` stays passive (P0 only) — safe. `-l 0` removes the per-source cap and follows providers to exhaustion — slow, loud toward *providers* (not the target), and apt to burn free-tier quota. Start capped (`-l 100`–`500`) and only lift the cap on a source you know is worth it.

**P1/P2 flags are active reconnaissance — gate them on program rules.** `-c` (DNS brute), `-n` (PTR across the /24), `-r` (resolution), `-t` (takeover HTTP), `-a` (API-path scan, follows redirects), `--vhost`, and `--screenshot` all generate traffic to DNS infrastructure or the hosts themselves. On a passive-only or OSINT-only program, these are forbidden. `-n` in particular sweeps the **entire /24** containing each discovered IPv4 — that /24 may contain out-of-scope neighbors, so confirm the range is yours before using it.

**Scope creep through correlation.** `--routeviews` and reverse DNS happily surface assets that *look* related but belong to a shared host, a CDN, or a different tenant. Routing and PTR relationships do not prove ownership. Every asset you act on must be independently confirmed in-scope — discovery breadth is a feature only until it walks you off the program.

**Takeover "indicators" are leads, not findings.** Reporting a raw `-t` hit without confirming the unclaimed resource exists is how hunters get reports closed as "not reproducible." Confirm the dangling target is actually registrable/claimable before you write it up.

**Stale and false results.** Passive sources return *historical* data: a host from `waybackarchive` or a CT log may be long dead, parked, or re-pointed. Resolve/re-request before asserting it is live. Sources also fail independently — a 0-count or failed source in the summary does not invalidate the run, but do not infer "no subdomains exist" from one source's silence.

**The stash persists — clean it up.** `~/.local/share/theHarvester/stash.sqlite` retains every run across sessions, including target names and discovered assets. Treat it as sensitive engagement data: include it (and `stash.schedules.sqlite`) in your post-engagement cleanup and retention procedures.

**Don't over-rely on raw output for dedupe-sensitive programs.** Everyone runs `crtsh`. Submitting a bare subdomain list as a finding invites duplicates. The report-worthy output is the *confirmed vulnerable* asset plus the provenance trail showing how an under-watched source exposed it.

---

## Reporting evidence

theHarvester's role in a report is **discovery provenance** — proof of how a public source exposed an asset, not proof of impact. Capture:

- **The exact command and source list.** `theHarvester -d example.com -b crtsh,subdomaincenter -f example-recon` — so the triager can reproduce the discovery.
- **The JSONL finding line, not just the value.** Each finding carries `type`, `value`, `sources` (list), and `actions` (list). The `sources` array *is* your provenance — it names which public dataset exposed the asset. Pull it cleanly:
  ```
  jq -c 'select(.type != "summary") | {type, value, sources, actions}' example-recon.jsonl
  ```
  For `dns-recursive-*` kinds the `value` is a JSON string — decode with `fromjson` in jq. Structured kinds add fields (`shodan-host` → `details`; `prefix` → `scope`, `observations`).
- **The summary line for run metadata.** Line 1 (`"type":"summary"`) carries `run_id`, `target`, `started_at`/`completed_at`, `result_count`, `counts` (by type), and `source_executions` (each with `source`, `status`, `result_count`, `duration_ms`, `stop_reason`, `error_type`). Quote the relevant `source_executions` entry to show the source succeeded and when.
- **Change evidence over time.** For a "newly exposed asset" claim, attach `harvest-report hostname-changes --target example.com` output showing the host in `newly_reported`, with the two `run_id`s dated.
- **Confirmation of the actual bug, separately.** The theHarvester output establishes discovery; the vulnerability PoC (the unclaimed takeover resource, the live dev-host response, the re-requested leaking endpoint) is captured with the appropriate tool and request/response pair. Keep the two clearly distinct so the triager sees both *how you found it* and *why it matters*.
- **Redaction.** Harvested emails and people names are personal data. Include only what the finding needs; never attach a full employee roster, and never present harvested contacts as anything other than scope-mapping context.

JSON and XML outputs are compatibility reports — a single JSON object (`cmd`, `hosts`, `shodan` always present; `vhosts` when present) and an XML tree (`<cmd>`, `<email>`, `<host>`, `<vhost>`). Neither records *which source* found each item, so prefer the JSONL for any evidence where provenance matters.

---

## References

- theHarvester repository and README (discovery-sources matrix, P0/P1/P2 classes) — https://github.com/laramies/theHarvester
- theHarvester `__main__.py` (argparse flag definitions, verbatim) — https://github.com/laramies/theHarvester/blob/master/theHarvester/__main__.py
- Wiki: Results and Local Data (JSONL schema, stash tables, `harvest-report` subcommands) — https://github.com/laramies/theHarvester/wiki/Results-and-Local-Data
- Wiki: Configuration and API Keys — https://github.com/laramies/theHarvester/wiki/Configuration-and-API-Keys
- Wiki: Responsible Use and Scope — https://github.com/laramies/theHarvester/wiki/Responsible-Use-and-Scope
- Wiki: Operator Workflows — https://github.com/laramies/theHarvester/wiki/Operator-Workflows
- `api-keys.yaml` (keyed source names) — https://github.com/laramies/theHarvester/blob/master/theHarvester/data/api-keys.yaml
- Handoff tooling: OWASP Amass — https://github.com/owasp-amass/amass · subfinder — https://github.com/projectdiscovery/subfinder

*Research supplement added 2026-11-15, grounded in theHarvester's official repository (README discovery-sources matrix, `__main__.py` argparse, and the Results-and-Local-Data / Configuration-and-API-Keys wiki pages fetched 2026-11-15) (model: claude-opus-4-8).*
