---
name: "subfinder"
categories:
  - blackarch-recon
version: "v2.14.0.r98.g66ba5e0e"
homepage: "https://github.com/projectdiscovery/subfinder"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-11-01"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/subfinder"
github-stars: 14743
github-pushed: "2026-11-06"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 205
gh-activity-checked: "2026-11-09"
---

# subfinder

> Fast passive subdomain enumerator from ProjectDiscovery, driven by a curated set of online sources.

## What it's for

subfinder discovers valid subdomains for a domain using only passive online sources (certificate transparency logs, threat-intel aggregators, code-hosting sites and similar). It is built for exactly one job — passive enumeration — and is deliberately lightweight and quiet: it does not brute-force DNS and does not touch the target's own infrastructure, which makes it a safe first step for attack-surface mapping and bug-bounty recon. Many of the best sources require API keys, which are configured once in a provider config file; the tool works without keys but returns fewer results. If you need active verification, DNS bruteforce or port scanning, pair it with a different tool rather than expecting it here.

## Typical use cases

### Basic passive enumeration

Discover subdomains for a single domain.

```
subfinder -d example.com
```

Outcome: queries all configured passive sources and prints every unique subdomain found for the domain.

### Clean list straight to a file

Produce a bare hostname list suitable for piping into other tools.

```
subfinder -d example.com -silent -o subs.txt
```

Outcome: writes only the subdomain names, one per line, to subs.txt with no banners or source noise.

### Exhaustive enumeration with all sources

Pull from every source, including those needing API keys.

```
subfinder -d example.com -all -oJ -o subs.jsonl
```

Outcome: enables all passive sources (slower), writing structured JSONL records — one per subdomain, including the source that reported it — to subs.jsonl.

### Select specific sources

Inspect or pick the data sources used.

```
subfinder -ls
subfinder -d example.com -s crtsh,github
```

Outcome: the first command lists every available source name and which need API keys; the second restricts enumeration to certificate transparency data and GitHub, making the run faster and more predictable.

### Filter results with regular expressions

Keep only hosts matching a naming pattern.

```
subfinder -d example.com -match-regex '^(api|web)[0-9]{1,3}\.' -filter-regex '(^|\.)dev\.'
```

Outcome: output is limited to subdomains starting with api/web and a number, dropping anything under .dev — useful to focus a follow-up scan on production-named assets.

### Enumerate a batch of root domains

Process many root domains from a file.

```
subfinder -dL domains.txt -oD results/
```

Outcome: enumerates each domain listed in domains.txt and writes one results file per domain into the results/ directory.

---

## subfinder's place in the pipeline (and the division of labour with amass)

subfinder is the **fast passive daily driver**; [[amass]] is the **deep graph miner**. They overlap on paper (both enumerate subdomains from passive sources) but you run them for different reasons, and a hunter who treats them as interchangeable wastes both:

| | subfinder | amass (`enum`) |
|---|---|---|
| Primary job | fast passive subdomain list | deep attack-surface graph |
| Runtime on one root | seconds to a couple of minutes | minutes to hours |
| State | **stateless** — each run is independent, prints to stdout | **persistent OAM graph** that compounds across runs |
| Active modes | DNS resolution only (`-active`) | brute force, alterations, zone transfers, cert grabs |
| Org→asset pivot | none | `assoc` / ASN+CIDR seeds |
| Differential | external (`anew`, `comm`) | native (`track -since`) |
| Reach for it | every day, in a pipe, for the clean name list | weekly/deep sweeps, org expansion, change tracking |

The practical split: **subfinder runs first and runs often** — it is cheap enough to pipe directly into `httpx` on a cron and alert on deltas. amass runs deeper and less often for the graph-and-expansion work subfinder deliberately does not do. On a fresh target, start with `subfinder -all` keyed, feed the names into liveness checks the same minute, and let amass grind in the background. Both read from overlapping passive sources, so **union their outputs** (`sort -u`, or feed both through the same `anew` file) rather than trusting either alone — each has sources the other lacks.

Everything below assumes an authorized program: subfinder is passive by default (it queries third-party data-source APIs and public datasets, **not the target's own infrastructure**), which makes it one of the safest recon tools to run, but "passive against the target" is not "no rules" — you are still bound by the program's scope, and you can still get an API key banned or a dataset provider annoyed. The two real disciplines are **scope** (never enumerate roots the program didn't authorize) and **provider etiquette** (don't hammer the data sources).

---

## Bug bounty workflow with subfinder

### Phase 0 — Keys and config (do this once, it is the whole game)

subfinder works keyless, but the keyless source set is a fraction of its reach. The sources that surface the long tail — the forgotten `admin-old.`, `jenkins-staging.`, `internal-api.` names other hunters miss — are almost all keyed. Provisioning keys *is* your competitive edge, exactly as it is with amass.

On first run subfinder writes `$HOME/.config/subfinder/provider-config.yaml` (overridable with `-pc` or `$SUBFINDER_PROVIDER_CONFIG`). Populate it:

```yaml
# $HOME/.config/subfinder/provider-config.yaml
# each key is a lowercase source name; value is a LIST (rotate multiple keys per source)
virustotal:
  - YOUR_VT_API_KEY
securitytrails:
  - YOUR_SECURITYTRAILS_KEY
shodan:
  - YOUR_SHODAN_KEY
github:
  - ghp_your_personal_access_token        # read-only PAT is enough; rotate several to beat rate limits
  - ghp_second_token_for_rotation
chaos:
  - YOUR_PDCP_CHAOS_KEY
censys:                                   # composite key: API_ID:API_SECRET
  - ac244e2f-...:dd510d6e-...
passivetotal:                             # composite: EMAIL:KEY
  - you@example.com:your_pt_key
fofa:                                     # composite: EMAIL:KEY
  - you@example.com:your_fofa_key
intelx:                                   # composite: HOST:KEY
  - 2.intelx.io:your_intelx_key
certspotter: []                           # keyed source left empty = skipped
```

Sources that **require a key** (per the install docs): BeVigil, BinaryEdge, BufferOver, C99, Censys, CertSpotter, Chaos, Chinaz, DNSDB, Fofa, FullHunt, GitHub, Intelx, PassiveTotal, Profundis, Quake, Robtex, SecurityTrails, Shodan, ThreatBook, VirusTotal, WhoisXML, ZoomEye, dnsrepo, Hunter, Facebook, BuiltWith, RedHunt Labs. Composite (colon-separated) keys: Censys, PassiveTotal, Fofa, Intelx, 360quake, Facebook (`APP_ID:APP_SECRET`), RedHunt Labs (`ENDPOINT:API_TOKEN`), ZoomEye (`zoomeye.hk`/`zoomeye.org:KEY`). Everything not in that list is keyless (CT-log scrapers, web archives, free aggregators) and runs with no setup.

Confirm what is actually live before you rely on it:

```
subfinder -ls                 # every source; keyed sources are flagged
subfinder -ls -oJ             # same, JSON — diff against last month to catch new sources
```

A source with an empty list or a missing key is silently skipped, so a half-filled config quietly under-enumerates. Verify, don't assume.

### Phase 1 — Scope lock-in

subfinder cannot wander out of scope the way amass can (it has no scope-expansion engine), but **you** can point it at the wrong roots. The discipline is simple and absolute: only pass roots the program's rules of engagement name.

```
subfinder -d example.com                    # one authorized root
subfinder -dL roots.txt -oD results/        # many roots, one file each under results/
```

`roots.txt` is one authorized root domain per line; `-oD` (works with `-dL` only) writes `results/example.com.txt`, `results/other.com.txt`, etc. Keep `roots.txt` in version control as your audit trail of exactly what you were authorized to enumerate. There is no `-df` flag here (that is amass) — subfinder's list input is `-dL`/`-list`. If your program lists IP ranges you must exclude from name-based results, `-ei`/`-exclude-ip` drops IPs from the domain list.

### Phase 2 — The fast passive sweep

The daily driver command, piped straight into liveness:

```
subfinder -d example.com -all -silent | httpx -silent -title -tech-detect -sc
```

`-all` turns on every configured source (it is slower and noisier — see Pitfalls — but on a first pass you want maximum coverage); `-silent` strips banners and the `[INF]` lines so stdout is pure hostnames, one per line, which is the only format a pipe wants. This is the canonical ProjectDiscovery pattern (`echo example.com | subfinder -silent | httpx -silent`), extended with httpx enrichment.

For a clean artifact plus structured data for later analysis, write both:

```
subfinder -d example.com -all -o subs.txt            # bare names, one per line
subfinder -d example.com -all -oJ -cs -o subs.jsonl  # JSONL, with per-name source attribution
```

`-oJ`/`-json` emits JSONL (one JSON object per subdomain); adding `-cs`/`-collect-sources` (JSON-only) records **which source(s) reported each name** — this is your source-verification signal: a host confirmed by three independent sources is almost certainly real; a host reported by one noisy scraper gets re-resolved before you trust it.

### Phase 3 — Recursive and targeted deepening

For nested subdomains (`staging.internal.example.com` under `internal.example.com`), `-recursive` restricts enumeration to the subset of sources that can walk subdomains recursively, so a name discovered in pass one becomes a query seed in pass two:

```
subfinder -d example.com -recursive -all -o subs-recursive.txt
```

To focus — or to make a run reproducible and cheap — pin specific sources or drop noisy ones:

```
subfinder -d example.com -s crtsh,github,virustotal        # only these three
subfinder -d example.com -all -es alienvault,waybackarchive # all sources except the loud ones
```

`-s`/`-sources` restricts to a named set; `-es`/`-exclude-sources` subtracts from the set. Names come from `-ls`.

### Phase 4 — Filtering to the assets you care about

Trim the raw list to production-named or in-scope hosts before it hits the vuln pipeline:

```
subfinder -d example.com -all \
  -match-regex '^(api|admin|internal|staging|dev)[.-]' \
  -filter-regex '(^|\.)(cdn|static|img)\.' \
  -o interesting.txt
```

`-match-regex` keeps only matching hosts; `-filter-regex` drops matching hosts (Go regex, repeatable, or one-per-line from a file); the plain `-m`/`-match` and `-f`/`-filter` do the same on literal subdomain strings. Filtering is a focus tool, not a scope tool — do your scope control at the `-d`/`-dL` level, never by hoping a regex excludes an out-of-scope root.

### Phase 5 — Optional active enrichment (resolution + IPs)

subfinder is passive up to this point. Two flags make it reach out to **DNS resolvers** (not to the target's web tier, but it is no longer purely "look it up in someone else's database"):

```
subfinder -d example.com -all -active -oI -o resolved.txt
```

`-nW`/`-active` keeps only subdomains that currently resolve (drops dead data-source names); `-oI`/`-ip` appends the resolved host IP — and `-oI` **requires `-active`**. `-t` (default 10) sets the concurrent resolver goroutines and, like `-oI`, only applies under `-active`. Use your own trusted resolvers so you neither poison results nor hammer one public resolver:

```
subfinder -d example.com -all -active -oI -r 1.1.1.1,8.8.8.8 -o resolved.txt
subfinder -d example.com -all -active -rL resolvers.txt -oI -o resolved.txt
```

In most workflows you skip `-active`/`-oI` and let `httpx`/`dnsx` downstream do resolution — but when you want a one-shot name+IP list without a second tool, this is it.

### Phase 6 — Differential monitoring (where the non-dupe money is)

subfinder has **no native `track`** (that is amass's edge). You get the same first-mover advantage with `anew` (tomnomnom) — "append lines from stdin to a file, but only if they don't already appear in the file; outputs new lines to stdout too." Run subfinder on a cron against a persistent baseline file; `anew` both updates the baseline and hands you *only the newly appeared names*:

```
# cron: every few hours against the authorized root
subfinder -d example.com -all -silent \
  | anew subs-known.txt \
  | httpx -silent -title -sc \
  | notify -silent          # alert only on net-new live hosts
```

The names `anew` prints are what showed up since your last sweep — a `payments-v2.example.com` the hour it ships, before the monthly-scanner crowd sees it, is the freshest and least-reviewed surface on the target. If you prefer no extra tool, diff two sorted snapshots:

```
subfinder -d example.com -all -silent | sort -u > today.txt
comm -13 yesterday.txt today.txt       # lines only in today = new subdomains
```

Automate Phases 2→6 and you have a continuous attack-surface monitor; the deltas are your low-dupe pipeline.

---

## Where the money is with subfinder

subfinder finds *surface*, never the vulnerability — severity is "whatever lives on the host it found." Be honest about dupes: **everyone runs `subfinder -d` on day one**, so a raw list is near-worthless as a submission. Your edge is keys other hunters don't have, recursion/filtering others skip, and differential timing.

| Finding class the asset enables | subfinder feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| Net-new host caught before other hunters | cron + `anew` / `comm` differential (Phase 6) | Varies — freshest code | **Low — your real edge** |
| Forgotten dev/staging/admin host | keyed sources (`-all`) + `-recursive` + `-match-regex` | Med–High | Medium |
| Subdomain takeover (dangling CNAME) | any passive source → `-active`/httpx → takeover check | High | High — first tool everyone runs |
| Deep nested internal name | `-recursive` on recursion-capable sources | Med–High | Medium |
| Host only one keyed source knows | a key competitors lack (Shodan/SecurityTrails/Censys/Chaos) | Varies | Low–Medium |
| Source-confirmed live API/panel | `-active -oI` + `-cs` attribution → httpx | Med–High | Medium |
| CT-log-only / wildcard-hidden name | keyless CT sources (default) | Varies | High |

The pattern mirrors amass: the **low-dupe rows are the config-and-cadence rows** (keyed sources, recursion, differential runs), not the bare `subfinder -d`. Compete on what you configured and how often you run, not on the one command everybody shares.

---

## Command recipes

Each block is a real invocation with the low-volume / in-scope settings called out. Output shown is representative.

**Fast keyless check — is this even worth deeper work?**

```
subfinder -d example.com -silent
```
```
www.example.com
api.example.com
blog.example.com
```
No keys, no `-all` — the quick keyless sources only. Good for a first look; expect it to miss the long tail.

**Full keyed sweep → liveness in one pipe (the daily driver).**

```
subfinder -d example.com -all -silent | httpx -silent -title -tech-detect -sc
```
```
https://api.example.com   [200] [Example API] [nginx]
https://blog.example.com  [301] [] [cloudflare]
```
`-all` = every configured source; `-silent` makes stdout pipe-clean. Resolve/probe *before* nuclei so you only template live hosts.

**Structured output with source attribution.**

```
subfinder -d example.com -all -oJ -cs -o subs.jsonl
```
```json
{"host":"api.example.com","input":"example.com","source":"crtsh"}
{"host":"api.example.com","input":"example.com","source":"virustotal"}
```
`-cs` (JSON-only) lists every source that reported a name — multi-source confirmation = high confidence; single noisy-source names get re-resolved before you trust them.

**Batch of authorized roots, one file each.**

```
subfinder -dL roots.txt -all -oD results/
```
`roots.txt` = one authorized root per line (keep it in git); `-oD` writes `results/<root>.txt` per domain and only works with `-dL`.

**Recursive deepening of nested names.**

```
subfinder -d example.com -recursive -all -o subs-recursive.txt
```
`-recursive` uses only sources that can enumerate `sub.sub.domain.tld`; pairs well with a second pass feeding discovered names back in.

**Targeted, reproducible, low-noise run.**

```
subfinder -d example.com -s crtsh,securitytrails,github -timeout 20 -max-time 5 -o subs.txt
```
`-s` pins sources (fast, predictable); `-timeout` (default 30s) bounds each source; `-max-time` (default 10 min) bounds the whole run so a slow provider can't hang your cron.

**Active resolution with IPs, your own resolvers.**

```
subfinder -d example.com -all -active -oI -r 1.1.1.1,8.8.8.8 -o resolved.txt
```
```
api.example.com,198.51.100.10
blog.example.com,198.51.100.23
```
`-active` drops dead names; `-oI` adds the IP (requires `-active`); `-r`/`-rL` point at trusted resolvers so you don't poison results or lean on one public resolver. `-t` (default 10) caps concurrent resolution.

**Provider rate-limiting — be a good API citizen.**

```
subfinder -d example.com -all -rl 10 -rls "shodan=15/s,github=5/s"
```
`-rl`/`-rate-limit` caps global HTTP requests/second across sources; `-rls` sets per-provider caps in `key=value` form. Use these when running `-all` repeatedly so you don't trip a provider's abuse detection and lose the key mid-engagement.

**Differential cron with alerting.**

```
subfinder -d example.com -all -silent | anew subs-known.txt | httpx -silent | notify -silent
```
`anew` updates the baseline and emits only net-new names; `notify` pings your channel only when something new goes live. This is the pipeline that earns the low-dupe rows above.

**Disable update checks / colour for clean cron logs.**

```
subfinder -d example.com -all -silent -duc -nc -o subs.txt
```
`-duc`/`-disable-update-check` stops the self-update probe (don't let a tool mutate itself mid-engagement); `-nc`/`-no-color` strips ANSI codes so log files stay greppable.

---

## Pitfalls, noise and scope discipline

**`-all` is louder and slower than it looks.** It enables *every* configured source, including slow, flaky, and heavily rate-limited ones, and sources with their own ToS on automated use. On a first pass the extra coverage is worth it; on a tight cron it can blow your `-max-time`, return more stale/false names, and burn API quota. For repeated runs, pin a known-good `-s` set or trim with `-es`, and cap with `-rl`/`-rls`. "Use all sources (slow)" is the tool's own description — treat it as a deliberate choice, not the default.

**Keyless ≠ complete.** Running without keys silently skips every keyed source — you'll get a plausible-looking list that is missing most of the interesting hosts, and you won't know it. A half-filled `provider-config.yaml` has the same failure mode: an empty list or a stale key is skipped with no error. Verify coverage with `-ls` and sanity-check counts against a keyed run.

**Scope lives at `-d`/`-dL`, nowhere else.** subfinder won't auto-expand scope, but nothing stops you handing it an out-of-program root (an acquired-company domain, a look-alike, a shared-hosting parent). Enumerating a root the program didn't authorize is a scope violation even though the queries went to third parties — you've now built an asset map of something you weren't allowed to touch. Only pass authorized roots; keep the `-dL` file under version control. Do **not** rely on `-match-regex`/`-filter-regex` for scope control — they filter output strings, they don't stop enumeration of the wrong root.

**Passive against the target ≠ no footprint at all.** Default subfinder sends no traffic to the target's infrastructure — but `-active` and `-oI` resolve names against DNS resolvers, which is observable to whoever runs those resolvers (and, for authoritative lookups, potentially the target's own name servers). It's low-risk, but if a program's rules say "no active techniques whatsoever," know that resolution crosses that line; stay purely passive and let a clearly-authorized downstream tool resolve.

**Provider etiquette protects your keys, not the target.** The abuse risk here is burning a Shodan/SecurityTrails/GitHub key or getting an IP rate-limited by a data source — which costs *you* the long tail for the rest of the engagement. Rotate multiple keys per source (the config value is a list), cap with `-rl`/`-rls`, and don't loop `-all` every five minutes.

**Stale and false-positive names.** Passive data-source names go dead; a name in a CT log from 2021 may not resolve today. Never report an asset as live off the raw subfinder line — re-resolve (`-active`, or `dnsx`/`httpx` downstream) and probe first. Reporting a dead name as a takeover is a fast way to lose triager trust.

**`-t`, `-oI`, `-oD` have activation conditions.** `-t` and `-oI` do nothing without `-active`; `-oD` does nothing without `-dL`. Setting them in the wrong combination fails silently — you just don't get the behaviour you expected. Match the flag to its mode.

**The version you have may not match the docs.** Help text and flag availability drift between releases (the running-docs banner still shows an old `v2.4.9` while current builds require go1.26). Confirm flags against *your* binary with `subfinder -h` before scripting them into a cron.

---

## Reporting evidence

subfinder output is almost never submitted as "subfinder found X" — it is the *input* that leads to a finding (takeover, exposed panel, net-new surface), and it doubles as proof you stayed in scope. Capture:

- **The exact command line**, including scope inputs (`-d example.com` or `-dL roots.txt`) and whether `-active` was used — this reproduces the result and shows the triager you only touched authorized roots passively.
- **The authorized-roots file** (`-dL` input) under version control — your record of what you were permitted to enumerate.
- **The JSONL output with `-cs` source attribution** for the specific host — showing which independent sources reported it establishes the name is real, not a one-source artifact.
- **Resolution proof** for any "live" claim: `-active -oI` output, or the downstream `httpx`/`dnsx` line, with the timestamp — never claim an asset is live off a raw passive name.
- **For a subdomain takeover:** the dangling chain (`dig CNAME <name>` → unclaimed provider) plus your controlled-claim PoC — the subfinder line only proves the name exists.
- **For a first-mover / net-new claim:** the `anew`/`comm` differential output and the date, showing the name appeared after your prior baseline.

Keep the proof minimal and in-scope: show the one asset and what makes it a finding, never a full enumeration dump, and don't include out-of-scope names you happened to see.

---

## Flag reference (grounded in `subfinder -h`)

Grouped as the help text groups them. Defaults and activation conditions are the common tripwires — note which flags do nothing outside their mode.

| Group | Flag (short, long) | Arg | Behaviour / note |
|---|---|---|---|
| INPUT | `-d`, `-domain` | `string[]` | roots to enumerate — **your scope boundary** |
| INPUT | `-dL`, `-list` | `file` | file of roots, one per line |
| SOURCE | `-s`, `-sources` | `string[]` | restrict to named sources (`-s crtsh,github`) |
| SOURCE | `-recursive` | — | only sources that enumerate nested subdomains |
| SOURCE | `-all` | — | every configured source (**slow, noisier**) |
| SOURCE | `-es`, `-exclude-sources` | `string[]` | subtract sources from the set |
| FILTER | `-m`, `-match` | `string[]` | keep matching subdomains (literal / file) |
| FILTER | `-f`, `-filter` | `string[]` | drop matching subdomains (literal / file) |
| FILTER | `-match-regex` | `string[]` | keep by Go regex (not for scope control) |
| FILTER | `-filter-regex` | `string[]` | drop by Go regex |
| RATE-LIMIT | `-rl`, `-rate-limit` | `int` | global max HTTP req/s across sources |
| RATE-LIMIT | `-rls` | `k=v` | per-provider caps (`"shodan=15/s"`) |
| RATE-LIMIT | `-t` | `int` | concurrent resolver goroutines, **`-active` only** (default 10) |
| UPDATE | `-up`, `-update` | — | self-update (avoid mid-engagement) |
| UPDATE | `-duc`, `-disable-update-check` | — | stop the update probe (use in cron) |
| OUTPUT | `-o`, `-output` | `file` | write output to file |
| OUTPUT | `-oJ`, `-json` | — | JSONL output |
| OUTPUT | `-oD`, `-output-dir` | `dir` | per-domain files, **`-dL` only** |
| OUTPUT | `-cs`, `-collect-sources` | — | per-name source attribution, **`-json` only** |
| OUTPUT | `-oI`, `-ip` | — | append resolved IP, **`-active` only** |
| CONFIG | `-config` | `file` | flag config file (default `$CONFIG/subfinder/config.yaml`) |
| CONFIG | `-pc`, `-provider-config` | `file` | provider/key file (default `$CONFIG/subfinder/provider-config.yaml`) |
| CONFIG | `-r` | `string[]` | resolvers to use |
| CONFIG | `-rL`, `-rlist` | `file` | resolver list file |
| CONFIG | `-nW`, `-active` | — | keep only resolving subdomains (enables resolution) |
| CONFIG | `-proxy` | `string` | HTTP proxy |
| CONFIG | `-ei`, `-exclude-ip` | — | drop IPs from the domain list |
| CONFIG | `-mr`, `-max-results` | `int` | cap results per source (0 = unlimited) |
| DEBUG | `-silent` | — | subdomains only on stdout (pipe mode) |
| DEBUG | `-v` | — | verbose (shows which sources answered) |
| DEBUG | `-version` | — | print version |
| DEBUG | `-nc`, `-no-color` | — | strip ANSI (cron logs) |
| DEBUG | `-ls`, `-list-sources` | — | list sources (keyed flagged); `-oJ` for JSON |
| OPTIMIZATION | `-timeout` | `int` | per-source timeout seconds (default 30) |
| OPTIMIZATION | `-max-time` | `int` | whole-run cap in minutes (default 10) |
| OPTIMIZATION | `-rsr`, `-response-size-read` | `int` | max response bytes read from a source (0 = unlimited) |

Env overrides: `SUBFINDER_CONFIG` (config.yaml path), `SUBFINDER_PROVIDER_CONFIG` (provider-config.yaml path). Flag availability drifts between releases — confirm against your binary with `subfinder -h`.

---

## References

- subfinder — official README (flags, provider config, features): https://github.com/projectdiscovery/subfinder/blob/main/README.md
- subfinder — usage / flag reference (docs): https://docs.projectdiscovery.io/tools/subfinder/usage
- subfinder — installation and provider-config.yaml (key format, sources needing keys): https://docs.projectdiscovery.io/tools/subfinder/install
- subfinder — running / post-install (pipe-into-httpx example): https://docs.projectdiscovery.io/tools/subfinder/running
- ProjectDiscovery httpx (standard liveness/pipeline partner): https://github.com/projectdiscovery/httpx
- tomnomnom `anew` (differential append for day-over-day monitoring): https://github.com/tomnomnom/anew
- companion deep-recon tool and graph/differential partner: [[amass]]

*Research supplement added 2026-11-15, grounded in the ProjectDiscovery subfinder README (`main` branch) and the official docs at docs.projectdiscovery.io/tools/subfinder (usage/install/running), with the `anew` differential pattern grounded in github.com/tomnomnom/anew; flag names and defaults verified against the upstream `-h` help block, and the division-of-labour framing cross-referenced with the companion [[amass]] note — the pre-existing "Typical use cases" above are preserved as-is (model: claude-opus-4-8).*
