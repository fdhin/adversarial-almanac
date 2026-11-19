---
name: "bbot"
categories:
  - blackarch-recon
version: "v3.0.2.r0.ga6fb827"
homepage: "https://github.com/blacklanternsecurity/bbot"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-09-06"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "blacklanternsecurity/bbot"
github-stars: 11002
github-pushed: "2026-11-06"
github-archived: false
github-issues: 34
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 1644
gh-activity-checked: "2026-11-09"
---

# bbot

> Preset-driven attack-surface scanner: one command runs subdomain enum, web spider, email gathering and more as a module pipeline.

## What it's for

BBOT (BEE·bot, Black Lantern Security) is a recursive, event-driven reconnaissance framework inspired by SpiderFoot. Scans are described by presets (`-p`), which select a set of modules and configuration — e.g. `subdomain-enum` combines passive API sources with recursive DNS brute-force, `spider` crawls the target site, and presets can be stacked on one command line. Results stream as typed events and can be sent to output modules (CSV, JSON, Neo4j, SQLite, Slack, Discord, …); each scan gets its own directory under `~/.bbot/scans/`. Custom Python modules and custom YAML preset files extend it, and it doubles as a Python library. Scope is enforced strictly: pass `-t` targets (domains, IPs, CIDRs, URLs) and everything discovered stays inside them unless you broaden scope.

## Typical use cases

### Enumerate subdomains

```
bbot -t example.com -p subdomain-enum
```

Outcome: runs passive API sources plus recursive DNS brute-force with NLP-powered subdomain mutations; unique subdomains are written to the scan's output files under `~/.bbot/scans/<scan_name>/` and streamed to the terminal.

### Restrict to passive sources only

```
bbot -t example.com -p subdomain-enum -rf passive
```

Outcome: `--require-flags` enables only modules tagged `passive` — no DNS brute-force or other active traffic touches the target, only API lookups.

### Crawl a site for emails and links

```
bbot -t example.com -p spider
bbot -t example.com -p email-enum subdomain-enum spider
```

Outcome: the first command runs the recursive web spider; the second stacks three presets in one scan, so discovered subdomains feed the spider and both feed email collection — maximum yield from a single run.

### Run a scan from a custom preset file

```
bbot -t example.com -p my_preset.yml --force
```

Outcome: loads the scan definition from your own YAML preset file instead of a built-in preset (module lists, flags, config and output modules all settable in the file); `--force` runs the scan even when preset condition violations or module setup failures occur, which is the escape hatch when a partially-broken optional module would otherwise abort the whole scan.

### Discover available modules, presets and flags

```
bbot -lp
bbot -l
```

Outcome: `-lp` lists the available presets (built-in and custom), `-l` lists all modules with their flags — the quickest way to see what a preset name will actually run before launching it.

### Run extra modules alongside a preset

Add specific modules to a named scan with its own output directory.

```
bbot -t example.com -p subdomain-enum -m naabu gowitness -n my_scan -o .
```

Outcome: runs the subdomain-enum preset plus the `naabu` port scanner and `gowitness` screenshotter; `-n` names the scan (default is random) so results land in a predictable directory, and `-o` sets the output root (here `./my_scan/`). Selecting the same module set by flag instead of preset name is equally valid: `-f subdomain-enum`.

### Check every discovered site for known secrets

Sweep badsecrets across a whole target as subdomains are found.

```
bbot -f subdomain-enum -m badsecrets -t example.com
```

Outcome: as the scan enumerates subdomains and fetches responses, the badsecrets module carves each HTTP response for known-key products (ASP.NET/JSF viewstate, framework cookies) and emits FINDING events for recoverable secrets — the at-scale counterpart to running the badsecrets CLI per URL. Feed it a custom key file with `-c modules.badsecrets.custom_secrets=reused_machinekeys.txt`.

*Command patterns sourced from HackTricks pages (CC BY-NC reference; rewritten) plus upstream documentation; module-selection and output-dir commands verified against the v3.x argparse source and current badsecrets README.*

---

## Bug bounty workflow with BBOT

BBOT is not a single scanner — it is a recursive, event-driven pipeline. Modules consume typed events (`DNS_NAME`, `IP_ADDRESS`, `IP_RANGE`, `OPEN_TCP_PORT`, `URL`, `URL_UNVERIFIED`, `HTTP_RESPONSE`, `EMAIL_ADDRESS`, `TECHNOLOGY`, `STORAGE_BUCKET`, `CODE_REPOSITORY`, `FINDING`, …), process them, and emit new events that feed other modules. A discovered subdomain becomes a `DNS_NAME` that the port scanner, the web probe, and the cloud-bucket checker all react to, so one `-t` seed fans out across the whole module graph automatically. For a bug bounty program the discipline is the opposite of the capability: the graph will happily walk out of scope and generate thousands of requests, so every phase below is about **constraining** the pipeline to the program's rules of engagement, then reading the results.

This is a map-the-surface tool, not an exploit tool. BBOT's payout contribution is almost always a **finding you then verify by hand** — a subdomain takeover candidate, an exposed bucket, a leaked secret, a forgotten admin host — not a self-contained PoC. Treat every `FINDING` as a lead requiring manual confirmation before you write the report.

> Scope note for the examples below: BBOT's own docs and README use `evilcorp.com`. Every command here is rewritten against `example.com`, `*.example.com`, and the TEST-NET-2 range `198.51.100.0/24` so nothing here points at a real target. Substitute your authorized program scope.

### Phase 0 — Translate the program scope into BBOT scope

Before any traffic, encode the program's rules of engagement into BBOT's three scope primitives. Getting this wrong is how hunters get banned — not the scan intensity, but hitting an out-of-scope asset.

- **`-t / --targets`** defines what is *in scope*. Accepted target types: `DNS_NAME`, `IP_ADDRESS`, `IP_RANGE` (CIDR, e.g. `198.51.100.0/24`), `OPEN_TCP_PORT`, `URL`, `EMAIL_ADDRESS`, `ASN`, `USERNAME`, `ORG_STUB`, `FILESYSTEM`, `MOBILE_APP`. By default child subdomains of a `DNS_NAME` target are in scope (`example.com` ⇒ `www.example.com` is in scope).
- **`--strict-scope`** turns that off: only exact hostname matches are in scope, no children. Use it when the program lists specific hosts rather than a wildcard domain. Note it applies to targets only, **not** to the blacklist.
- **`-b / --blacklist`** takes ultimate precedence — anything matching is never touched even if it is otherwise in scope. This is where you encode the program's *exclusions* (acquisitions out of scope, a staging host that must not be hit, `signout`/`logout` paths). The blacklist is the **only** scope input that accepts a regex, prefixed `RE:` and matched against the full URL including host and path:

```
bbot -t example.com -b "RE:(signout|logout|delete)" --strict-scope
```

- **`-s / --seeds`** feeds passive modules a starting point without putting it in scope (defaults to the targets). Useful when you want API lookups seeded from a value you are not authorized to actively touch.

There is **no** `-w`/`--whitelist` flag and **no** target-regex: BBOT scope is host/network based, with regex reserved for the blacklist. (If you have seen advice to run `-p safe` with a "target regex," it conflates two things that do not exist — the correct controls are `-rf safe` for module safety, below, and `-b "RE:..."` for scope exclusions.)

Confirm the resolved scope before launching with a dry run, which assembles the full config and module list and then aborts without sending traffic:

```
bbot -t example.com -b "RE:signout" -p subdomain-enum --dry-run
bbot -t example.com -p subdomain-enum --current-preset        # print the effective preset as YAML
```

### Phase 1 — Passive-only discovery (zero packets to the target)

Start with no active traffic. BBOT's `passive` flag tags ~69 modules that only query third-party APIs and datasets (certificate transparency, Chaos, passive DNS, etc.) — nothing reaches the target's own infrastructure:

```
bbot -t example.com -p subdomain-enum -rf passive -om json -n ex_passive -o .
```

- `-rf passive` (`--require-flags`) keeps **only** modules tagged `passive`, overriding the preset's active members. This is the safest possible recon and a good first pass on any program.
- `-p subdomain-enum` selects the subdomain-enumeration module set (48 modules carry the `subdomain-enum` flag).
- `-om json` adds the JSON output module on top of the always-on `txt`/`csv`; `-n` names the scan so results land in `./ex_passive/` instead of a random directory.

The output is a deduplicated, normalized set of in-scope subdomains and the emails/ASNs/cloud resources those passive sources revealed. Dedupe and normalization are a real part of the value here: the same host surfaced by crt.sh, a passive-DNS feed, and a wordlist collapses to one `DNS_NAME` event, and BBOT resolves and tags it rather than handing you three raw lines to reconcile.

### Phase 2 — Controlled active enumeration

Only once passive recon is in hand, and only if the program permits active traffic, enable active modules — but cap their intensity. The default DNS brute-force settings are tuned for speed, not for a shared bug-bounty target.

```
bbot -t example.com -p subdomain-enum \
  -c dns.brute_threads=100 dns.brute_rate_limit=50 \
  -n ex_active -o .
```

- `dns.brute_threads` defaults to **1000** and `dns.brute_rate_limit` defaults to **0 (unlimited)**. On a live program, dial both down: the command above caps brute-force at 50 queries/second with 100 threads. The `dnsbrute` module resolves a static wordlist (`modules.dnsbrute.wordlist`); `dnsbrute_mutations` adds target-specific mutations.
- To stay entirely on safe modules while still allowing active DNS, use `-rf safe` (103 modules are tagged `safe` = "non-intrusive and non-destructive") and/or `-ef loud invasive` to exclude the ~21 `loud` and 8 `invasive` modules (`dotnetnuke`, `generic_ssrf`, `legba`, `lightfuzz`, `medusa`, `nowafpls`, `nuclei`, `telerik`).

Port scanning is a separate, louder step. The `portscan` module uses masscan and scans the top 100 ports by default; throttle it explicitly and keep it inside the authorized network range:

```
bbot -t 198.51.100.0/24 -m portscan \
  -c modules.portscan.rate=150 modules.portscan.top_ports=100 \
  -n ex_ports -o .
```

`modules.portscan.rate` defaults to 300 packets/second — halve it on shared infrastructure. Only scan IP ranges the program explicitly authorizes; a CIDR target that includes a cloud provider's shared addresses is a scope violation waiting to happen.

### Phase 3 — Web content, technology, and secret surface

Feed the hosts found in Phases 1–2 into the web modules. The `http` module (native Rust `blasthttp` engine) probes URLs; `gowitness` screenshots them; `badsecrets` carves responses for known-key products; `trufflehog` and `gitdumper` chase leaked credentials and exposed `.git` folders; `baddns` flags subdomain-takeover candidates.

```
# light web pass, rate-limited, with screenshots and secret detection
bbot -t example.com -p web web-screenshots \
  -m badsecrets baddns \
  -c web.http_rate_limit=20 web.spider_distance=1 web.spider_depth=2 \
  -n ex_web -o .
```

- `web.http_rate_limit` defaults to **0 (unlimited)** and applies globally across HTTP consumers — set it (20 req/s above) on any live program.
- Spidering is bounded by `web.spider_distance` (links followed in a row, default 0), `web.spider_depth` (directory depth, default 1), and `web.spider_links_per_page` (default 25). The `spider` preset raises these aggressively (distance 2, depth 4); only use `-p spider`/`spider-heavy` when crawl volume is acceptable to the program.
- Chain a previous scan's output as this scan's seed by pointing `-t` at the `subdomains.txt` from Phase 1, so you only probe hosts you already confirmed in scope.

### Phase 4 — Output, triage, and verification

BBOT writes `output.txt`, `output.csv`, and `output.json` to `~/.bbot/scans/<name>/` (or under your `-o` directory) by default. For triage, the JSON stream and a graph database are the two highest-leverage formats:

```
# stream JSON to jq to pull just the findings with their severity
bbot -t example.com -p subdomain-enum -om json -n ex -o . \
  && jq -r 'select(.type=="FINDING") | "\(.data.severity // "n/a")\t\(.data.description)"' \
     ex/ex/output.json | sort
```

Every `FINDING` carries a `severity` (INFO / LOW / MEDIUM / HIGH / CRITICAL) and a confidence level. **In BBOT 3.0 the separate `VULNERABILITY` event type was removed and merged into `FINDING`** — so on this v3.x build, filter on `FINDING` and read `severity`, not on a `VULNERABILITY` type. Sort findings by severity, then hand-verify each before reporting.

For a large program, push into Neo4j and explore the asset graph visually (relationships between hosts, IPs, buckets, and emails are where chained findings hide):

```
# Neo4j via docker (docs' default creds neo4j / bbotislife), then:
bbot -t example.com -p subdomain-enum kitchen-sink -om neo4j -n ex -o .
```

The `python` output module exists specifically for library use — when you drive BBOT from `bbot.scanner.Scanner`, it yields events to your code as they are produced:

```python
# -I isolates the interpreter; run outside any untrusted download directory
from bbot.scanner import Scanner

scan = Scanner("example.com", presets=["subdomain-enum"],
               config={"dns": {"brute_rate_limit": 50}})
for event in scan.start():
    if event.type == "FINDING":
        print(event.data.get("severity"), event.data.get("description"))
```

This is the right integration point for feeding BBOT into your own triage pipeline, deduping across scans, or correlating with other tools — far cleaner than re-parsing text output.

### Phase 5 — Continuous, resource-disciplined monitoring

New subdomains are the freshest, least-reviewed surface on any program. Re-running the passive preset on a schedule and diffing `subdomains.txt` between runs surfaces newly shipped hosts before anyone else looks at them. Reusing the same `-n <name>` appends to the existing scan directory; BBOT keeps the most recent 20 scans (`keep_scans: 20`). For long or scheduled scans, cap resource use: `max_mem_percent` defaults to 90, `redact_secrets` defaults to true (keep it on so captured secrets are not written verbatim to disk), and the rate/thread caps from Phases 2–3 keep a background scan from tripping the program's WAF or rate limiters.

---

## Where the money is with BBOT

BBOT finds *surface and leads*; the payout comes from what you do with each lead. Priority by expected bounty value:

| Priority | Finding class | Module / feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Subdomain takeover | `baddns` (`-p baddns`/`baddns-heavy`), `subdomain-hijack` flag | High–Critical | Medium — scanned often; be fast on fresh subdomains |
| 2 | Leaked secrets / credentials | `trufflehog`, `badsecrets`, `gitdumper`, `git` leaks via `code-enum` | High–Critical | Medium — depends on freshness |
| 3 | Exposed cloud storage (buckets) | `-p cloud-enum` (`STORAGE_BUCKET` events, 16 `cloud-enum` modules) | Medium–High | High — buckets are heavily enumerated |
| 4 | Forgotten / dev / admin hosts | `subdomain-enum` + `http` probe + `gowitness` triage | Medium–High | Medium |
| 5 | Exposed `.git`, env, backup files | `gitdumper`, `httpx`/`http` + `webbrute` content discovery | High | Medium |
| 6 | Nuclei-matched known CVEs/misconfig | `-p nuclei`/`nuclei-recommended` (rate-limited) | Low–Critical (template-dependent) | **High** — everyone runs nuclei; verify + add impact |
| 7 | Technology/version inventory → CVE pivot | `-p tech-detect`, `TECHNOLOGY` events | Info → leads | High on its own |
| 8 | Exposed panels / API docs / swagger | `http`, `web_report`, screenshots | Low–Medium | High |
| 9 | Email / employee enum → phishing/cred-stuffing context | `-p email-enum`, `affiliates` flag | Info–Low | High |
| 10 | Virtual-host / SNI-hidden apps | `-p virtualhost` | Medium | Low–Medium — under-tested |

The two columns that decide whether a lead is worth writing up are **severity** and **dupe risk**. Anything a one-liner nuclei scan finds (priority 6) is almost certainly already reported — it only becomes a payout when you chain it or demonstrate concrete impact. The under-contested wins are takeovers caught early (1), fresh secrets (2), and virtualhost-hidden apps (10), because they depend on *timing* and *thoroughness* rather than everyone's default template pack.

---

## Command recipes

Annotated command → expected output, with the low-volume settings called out. Every flag below is verified against the current CLI help and configuration docs.

**1. Passive-only subdomain map (safest first pass):**

```
bbot -t example.com -p subdomain-enum -rf passive -om json -n ex -o .
```

→ Only `passive`-flagged modules run (API/dataset lookups, zero packets to the target). Writes `./ex/ex/{output.txt,output.csv,output.json}`; `subdomains.txt` holds the deduped, resolved, in-scope hosts. Expect dozens–hundreds of `DNS_NAME` events and some `EMAIL_ADDRESS`/`ASN`/`STORAGE_BUCKET` events, each tagged with its source module.

**2. Rate-limited active subdomain brute-force:**

```
bbot -t example.com -p subdomain-enum \
  -c dns.brute_threads=100 dns.brute_rate_limit=50 \
  -ef loud invasive -n ex -o .
```

→ Adds `dnsbrute`/`dnsbrute_mutations` to the passive set but caps brute at 50 q/s (default is unlimited, 1000 threads) and excludes `loud`/`invasive` modules. Expect additional `DNS_NAME` events from wordlist + mutation guessing.

**3. Throttled port scan of an authorized range:**

```
bbot -t 198.51.100.0/24 -m portscan \
  -c modules.portscan.rate=150 modules.portscan.top_ports=100 -n ex -o .
```

→ masscan at 150 pkt/s (default 300) over the top 100 ports, yielding `OPEN_TCP_PORT` events. Only ports 80/443 are kept on CDNs/WAFs unless you pass `--allow-cdn-ports`.

**4. Light web pass with screenshots + secrets, rate-limited:**

```
bbot -t example.com -p web web-screenshots -m badsecrets \
  -c web.http_rate_limit=20 web.spider_depth=2 web.spider_distance=1 -n ex -o .
```

→ Probes discovered URLs at 20 req/s (default unlimited), screenshots them (`WEBSCREENSHOT` events), carves responses for known secrets (`FINDING` events from `badsecrets`). Keep spider distance/depth low so the crawl does not explode.

**5. Cloud resource / bucket enumeration:**

```
bbot -t example.com -p cloud-enum -om json -n ex -o .
```

→ Runs the 16 cloud modules, emitting `STORAGE_BUCKET` and related events for buckets, Azure tenants, etc. Verify each bucket's permissions by hand before reporting — enumeration ≠ exposure.

**6. Subdomain-takeover sweep (high-value, low-noise):**

```
bbot -t example.com -p subdomain-enum baddns -om json -n ex -o .
```

→ Enumerates subdomains, then `baddns` checks each for dangling CNAMEs / takeover conditions, emitting `FINDING` events with severity. Confirm manually (claim the resource in a sandbox you control, never leave a live takeover) before reporting.

**7. Rate-limited nuclei pass (expect dupes):**

```
bbot -t example.com -p nuclei-recommended \
  -c modules.nuclei.ratelimit=50 modules.nuclei.concurrency=10 -n ex -o .
```

→ ProjectDiscovery's curated template set at 50 req/s (default 150) and 10 concurrent (default 25). Treat every hit as likely-duplicate until you add demonstrated impact. `nuclei` is an `invasive`-flagged module — only run on programs that permit it.

**8. Inspect before you launch:**

```
bbot -lp                 # list presets
bbot -lf                 # list flags (safe/passive/active/loud/invasive/...)
bbot -l                  # list all modules
bbot -f subdomain-enum -l   # list only the modules a flag selects
bbot ... --dry-run       # assemble config + module list, then abort (no traffic)
bbot ... --current-preset   # print the effective preset as YAML
```

→ Always confirm *which modules will actually run* before launching against a live program. `--dry-run` is the single most important safety check.

**9. A reusable, program-scoped preset (encode the rules of engagement once):**

A preset is a YAML file with `targets`, `blacklist`, `flags`, `modules`, `exclude_modules`, `include` (to stack other presets), `output_modules`, and `config` keys. Encoding a program's scope and your safe rate caps into one file means you cannot forget them on the next run:

```yaml
# example-program.yml  — authored from the program's scope declaration
description: Authorized recon for Example program (safe, rate-capped)
targets:
  - example.com
  - 198.51.100.0/24
blacklist:
  - "RE:(signout|logout|/delete)"   # program-excluded paths (regex is blacklist-only)
  - staging.example.com             # explicitly out-of-scope host
include:
  - subdomain-enum                  # stack built-in presets
  - cloud-enum
require_flags:
  - safe                            # only non-intrusive, non-destructive modules
exclude_flags:
  - loud
  - invasive
output_modules:
  - json
  - neo4j
config:
  dns:
    brute_threads: 100
    brute_rate_limit: 50
  web:
    http_rate_limit: 20
```

```
bbot -p ./example-program.yml -n example_run -o . --dry-run   # verify, then drop --dry-run
```

→ One command, one file, scope + exclusions + safe-module gating + rate caps + outputs all pinned. Re-run it on a schedule for monitoring (Phase 5). **Caution:** a BBOT preset "is executable code, not just data" — it can reference custom Python modules and run conditions, so only load preset files you wrote or trust, and BBOT will prompt before trusting a custom module folder (`-y` skips that prompt — don't use it on untrusted presets).

---

## Pitfalls, noise and scope discipline

How hunters get reports rejected — or get banned — with BBOT, and the settings that prevent it:

- **Scope leakage via default subdomain inclusion.** `-t example.com` puts *every* discovered child subdomain in scope and in the crawl. If the program scopes specific hosts only, use `--strict-scope`, and encode exclusions in `-b` (the only regex-capable input). A single request to an out-of-scope acquisition is a classic ban.
- **Unlimited default rates.** `dns.brute_rate_limit`, `web.http_rate_limit`, and the brute thread count (1000) default to fast/unlimited. On a live target these trip WAFs and rate limiters, poison your results with 429s (`web.429_sleep_interval` kicks in, slowing everything), and can look like an attack. Always set explicit caps on a program (Recipes 2 and 4).
- **Running `loud`/`invasive` modules unknowingly.** `-p kitchen-sink` ("everything everywhere all at once") pulls in `nuclei`, fuzzing, and aggressive crawling. Before any broad preset, run `--dry-run` and `-lf`, and gate with `-ef loud invasive` or `-rf safe` unless the program explicitly permits intrusive testing.
- **masscan at default rate on shared infrastructure.** `modules.portscan.rate=300` can saturate small targets. Lower it, scan only authorized CIDRs, and remember BBOT keeps only 80/443 on CDNs unless you opt into `--allow-cdn-ports` (which can scan a shared CDN edge — usually out of scope).
- **Reporting raw BBOT output as a finding.** A `FINDING` event is a lead, not a vulnerability. Nuclei hits, exposed-panel notes, and tech-inventory lines are mostly duplicates or informational. Triagers reject "I ran a scanner and here is the log." Verify, reproduce by hand, and frame impact.
- **Trusting `-p safe` / target regex that don't exist.** There is no `safe` preset and no regex on targets. The real controls are `-rf safe` (module flag) and `-b "RE:..."` (blacklist). Copy-pasting a non-existent flag silently does nothing or errors.
- **Secret handling.** `redact_secrets` defaults to true — leave it on so captured credentials are not written verbatim into the scan directory, and treat `~/.bbot/scans/` as sensitive. Never exfiltrate more than the PoC needs.
- **DNS wildcard noise.** Wildcard DNS makes brute-force emit garbage subdomains; BBOT detects wildcards by default (`dns.wildcard_disable: False` keeps detection on). If a target's results look implausibly large, confirm wildcard handling before reporting any host as real.

---

## Reporting evidence

What to capture from a BBOT run and put in the report:

- **The exact invocation.** The full `bbot` command including `-t`, `-b`, scope flags, and the rate/thread caps you used — this proves you stayed within rate limits and scope.
- **The scan's `preset.yml`.** BBOT writes the fully-resolved preset into the scan directory; attaching it documents precisely which modules ran.
- **The specific `FINDING` event(s), from `output.json`.** Include the event's `type`, `data.description`, `data.severity`, `data.host`/`url`, and the `module` that produced it — not the whole scan log. One JSON object per finding.
- **Manual verification steps.** The scanner located it; the report must show you confirmed it. For a takeover: the dangling CNAME and your (sandboxed) proof you could claim it. For a bucket: the exact `curl`/CLI command and its listing/response. For a leaked secret: redacted proof it is valid, obtained minimally.
- **Request/response pair** for the confirmed issue, captured by hand (curl with `-i`), with the sensitive values redacted to the minimum needed to prove impact.
- **Screenshots** from the `gowitness`/`web-screenshots` output for exposed panels or apps, cropped to the evidence.
- **Scope attestation.** State the in-scope target and that `-b`/`--strict-scope` kept the scan inside the program's rules; note anything you deliberately did *not* touch.

Structure each finding impact-first ("Subdomain takeover on `dev.example.com` via dangling CNAME to an unclaimed provider resource"), then preconditions, then the BBOT lead + your manual PoC, then remediation. The BBOT output is your discovery provenance; the hand-verified PoC is the product.

---

## BBOT vs. a hand-rolled recon pipeline

The usual alternative is a shell pipeline: `subfinder`/`amass` → `dnsx` → `httpx` → `naabu` → `nuclei`, glued with `anew`, `jq`, and files. BBOT competes with that pattern directly, and the tradeoff is worth naming explicitly because it decides when to reach for which.

**What BBOT buys you over the shell pipeline:**

- **Recursion for free.** In a hand-rolled pipeline each stage runs once; a subdomain discovered by `httpx` in stage 3 does *not* loop back through subdomain enumeration. BBOT's event bus re-feeds every new `DNS_NAME`/`URL`/`IP_ADDRESS` into every interested module until the graph quiesces, so a subdomain found by crawling a page gets port-scanned and bucket-checked automatically. Replicating that by hand means a `while`-loop with your own dedupe and fixpoint logic.
- **Dedupe and normalization built in.** The same host from crt.sh, passive DNS, and brute-force collapses to one typed, resolved, tagged event. The shell equivalent is `sort -u` across mismatched formats (FQDNs vs URLs vs `host:port`) and losing provenance in the process.
- **Unified, structured output.** One `output.json` event stream, plus CSV/Neo4j/SQLite/Postgres/Splunk sinks and a Python event bridge — versus N tools each with its own format you reconcile afterward.
- **Scope enforcement across the whole graph.** `-t`/`-b`/`--strict-scope` apply to *everything* the pipeline discovers, not just the first stage. In a shell pipeline you re-filter scope at every hop or risk a later tool wandering out of bounds.

**What the shell pipeline buys you over BBOT:**

- **Per-stage control and visibility.** You see and tune each tool's exact flags, and you can swap in the newest version of any one tool the day it ships. BBOT wraps specific module versions; a bleeding-edge `nuclei` flag may not be exposed yet.
- **Lower blast radius by default.** A pipeline runs exactly the stages you typed. BBOT's power is also its risk: a broad preset fans out fast, so you must actively constrain it (`--dry-run`, `-rf safe`, rate caps) to match what a careful manual pipeline does by construction.
- **Simpler mental model for a one-off.** For "just the subdomains of one host," `subfinder -d example.com` is less to reason about than a preset + flag + config triad.

**Practical rule:** reach for BBOT when you want *breadth and recursion* across a whole program with unified, deduped output and graph-level scope control — the monitoring and asset-inventory use cases. Reach for the hand-rolled pipeline when you want *surgical control* over a single stage, the very latest build of one tool, or a minimal-footprint probe of one asset. Many hunters use both: BBOT for the recursive surface map and continuous monitoring, individual tools for deep-diving a specific lead it surfaced. BBOT itself blurs the line — it can call masscan (`portscan`), nuclei, trufflehog, and gowitness as modules, so "BBOT vs. the tools" is often really "BBOT orchestrating the tools vs. you orchestrating them."

---

## References

- BBOT GitHub repository & README — https://github.com/blacklanternsecurity/bbot
- BBOT docs — Scanning overview (events, scope, presets) — https://www.blacklanternsecurity.com/bbot/Stable/scanning/
- BBOT docs — Advanced usage / full command-line help — https://www.blacklanternsecurity.com/bbot/Stable/scanning/advanced/
- BBOT docs — List of presets — https://www.blacklanternsecurity.com/bbot/Stable/scanning/presets_list/
- BBOT docs — Presets (how they work) — https://www.blacklanternsecurity.com/bbot/Stable/scanning/presets/
- BBOT docs — Output modules — https://www.blacklanternsecurity.com/bbot/Stable/scanning/output/
- BBOT docs — Configuration (global + module config keys) — https://www.blacklanternsecurity.com/bbot/Stable/scanning/configuration/
- BBOT docs — Tips and tricks (spider settings, proxy) — https://www.blacklanternsecurity.com/bbot/Stable/scanning/tips_and_tricks/
- BBOT docs — Events (types, FINDING severity) — https://www.blacklanternsecurity.com/bbot/scanning/events
- BBOT docs — List of modules — https://www.blacklanternsecurity.com/bbot/Stable/modules/list_of_modules/
- BBOT docs — Scanner Python API (`bbot.scanner.Scanner`) — https://www.blacklanternsecurity.com/bbot/Stable/dev/scanner/
- BBOT 3.0 release notes (VULNERABILITY merged into FINDING) — https://blog.blacklanternsecurity.com/p/bbot-30

*Research supplement added 2026-11-16, grounded in the official BBOT documentation and GitHub repository listed above (every flag, config key, module and event type verified against the Stable docs and v3.x CLI help; `-p safe`/target-regex corrected to the real `-rf safe` + `-b "RE:"` controls; VULNERABILITY→FINDING merge confirmed against the 3.0 notes). Earlier "Typical use cases" command patterns remain sourced from HackTricks (CC BY-NC; rewritten) plus upstream docs. (model: claude-opus-4-8).*
