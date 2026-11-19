---
name: "amass"
categories:
  - blackarch-scanner
version: "v5.1.1.r0.g79299dc"
homepage: "https://github.com/owasp-amass/amass"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "owasp-amass/amass"
github-stars: 15487
github-pushed: "2026-11-09"
github-archived: false
github-issues: 250
github-match: "exact"
gh-checked: "2026-11-09"
worth: 9.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 300
gh-activity-checked: "2026-11-09"
---

# amass

> OWASP Amass: the reference in-depth attack-surface mapper — subdomain enumeration, DNS mining and network mapping from dozens of open-source data sources.

## What it's for

Amass (an OWASP flagship project, now hosted under the owasp-amass GitHub organization)
maps external assets. The `enum` subcommand discovers subnames through APIs, certificate
transparency logs, web archives, scraping, DNS brute forcing, name alterations/permutations
and zone transfers, and stores every finding in a local graph database that persists and
compounds across runs. `intel` flips the view to the organization — ASNs, CIDRs, reverse
WHOIS — to find root domains you did not know a company owned, and `db` manages the stored
graph. Enumerations can resolve addresses (`-ip`), export text/JSON (`-oA`), or run in a
strictly passive mode (`-passive`) that never touches the target's name servers. The
package also ships reusable data files (name lists, example config) under
`/usr/share/amass/resources/`.

## Typical use cases

### Enumerate subdomains of a domain

The standard starting run.

```
amass enum -d example.com
```

Outcome: runs the default enumeration — OSINT sources plus DNS resolution — printing
discovered FQDNs and storing them in the graph database under `~/.config/amass` so later
runs build on previous findings.

### Run a strictly passive enumeration

Stay invisible to the target's infrastructure.

```
amass enum -passive -d example.com
```

Outcome: uses only sources that never query the target's name servers (CT logs, APIs,
archives) — the safest mode for stealth-sensitive engagements, at the cost of missing
DNS-only names.

### Brute force with recursion and a custom wordlist

Add active DNS discovery on top of the OSINT sources.

```
amass enum -brute -w wordlist.txt -min-for-recursive 2 -d example.com
```

Outcome: DNS brute forcing over `wordlist.txt` that recurses into newly found subdomains
once they are 2 labels deep (default 1), typically surfacing internal-only names the
passive sources miss.

### Save full results with IP resolution

Produce files downstream tooling can consume.

```
amass enum -ip -oA amass_scan -d example.com
```

Outcome: writes `amass_scan.txt` and `amass_scan.json` with each discovered name annotated
with its resolved addresses, alongside the graph database output.

### Find root domains owned by an organization

Expand scope before enumerating.

```
amass intel -whois -d example.com
```

Outcome: reverse-WHOIS pivot that lists other root domains registered to the same
organization; `amass intel -org "Example Org"` does the same starting from an AS
description string.

### Reuse the bundled name lists with other tools

Feed Amass's packaged data to fuzzers.

```
ffuf -w /usr/share/amass/resources/namelist.txt -u http://target.example.com -H "Host: FUZZ.target.example.com"
```

Outcome: the subdomain name list shipped with the package drives vhost brute forcing in
ffuf, printing hosts that answer differently from the default site. (Older amass packages
shipped this data as `wordlists/bitquark_subdomains_top100k.txt` under `/usr/share/amass`;
the v5 reorganization moved it to `resources/`.)

---

## Version note before anything below (v4 → v5)

Most Amass tutorials online — and the "Typical use cases" above — describe **v3/v4**. The packaged binary here is **v5.1.1**, which is a different CLI. If you copy a blog command, it will often be wrong. The changes that matter for bug bounty:

| v3/v4 (what blogs show) | v5.1.x (what you have) |
|---|---|
| `amass intel -whois -org ...` | **`intel` subcommand removed.** Org→asset work is now seed scope (`asns`/`cidrs`/`domains` in `config.yaml`) + the `assoc` subcommand. |
| `amass db` manages the graph | **`db` subcommand removed.** Results are read with `subs`/`track`/`assoc`/`viz`. |
| Normal mode resolves by default; `-passive` opts out | **Passive is the default**; `-passive` is deprecated. You opt *in* to active work with `-active` and `-brute`. |
| `amass enum -ip -oA out` | enum has **no `-ip`** (use `-addr` for IP scope, and read IPs back with `amass subs -ip`); `-oA` still writes files. |
| `-dns-qps` / `-max-dns-queries` throttle | **No QPS flag in the v5 `enum` CLI.** Footprint is governed by `-active`/`-brute`, wordlist size, `-max-depth`, and your resolver pool instead. |
| API keys in `config.ini` | Keys live in **`datasources.yaml`**, referenced from `config.yaml`'s `options.datasources`. |

The exact v5 subcommand set is `assoc | engine | enum | subs | track | viz` plus top-level `-version`/`-help`. Everything below is written for that binary. When in doubt, `amass <subcommand> -h`.

---

## Bug bounty workflow with amass

Amass sits at the **head of the recon pipeline**: it converts a program's scope statement into a validated, deduplicated inventory of assets that later tools (httpx, nuclei, port scanners) act on. It does not find vulnerabilities — it finds *where to look*. The discipline is to expand coverage aggressively inside scope while guaranteeing you never touch anything outside it.

### Phase 0 — Authorization and scope lock-in

Before any query leaves your box, translate the program's rules of engagement into Amass's scope model. This is the single most important step, because **Amass expands scope by default** — its whole purpose is to discover assets you didn't know about, and left unconstrained it will happily walk into domains and netblocks the program never authorized.

Encode the in-scope roots and the hard boundaries in `config.yaml`:

```yaml
# ~/.config/amass/config.yaml
scope:
  domains:
    - example.com                 # in-scope registered domains only
  asns:
    - AS64496                      # only if the program lists ASNs as in scope
  cidrs:
    - 198.51.100.0/24             # only if explicitly in scope
  ports:
    - 443
    - 80
  blacklist:
    - internal.example.com        # subnames you must never investigate
    - legacy.example.com

options:
  datasources: "./datasources.yaml"
  active: false                    # no active techniques unless the program allows them
  rigid_boundaries: true           # DO NOT auto-expand scope — bounty-critical
  bruteforce:
    enabled: false
  alterations:
    enabled: false
```

`rigid_boundaries: true` (equivalently the `-rigid` flag, *"disable scope expansion"*) is the line that keeps you in scope. Without it, Amass follows associations outward and a `*.example.com` program can turn into requests against `example-cdn.net`, acquired-company domains, and shared-hosting neighbours — exactly the "touched an out-of-scope asset" write-up that gets reports closed and accounts banned. Treat `blacklist` as a safety net, not a strategy: enumerate only the roots the program names.

### Phase 1 — Organisation → asset expansion (the intel phase)

Classic `amass intel -whois`/`-org` no longer exists. In v5 the org-pivot happens two ways:

1. **Seed what the program authorizes.** If the program scopes ASNs or CIDRs, put them in `scope.asns`/`scope.cidrs` (or `enum -asn 64496 -cidr 198.51.100.0/24`). Discover the ASN out-of-band first (BGP.he.net, bgp.tools, PeeringDB, RIR WHOIS) — confirm it actually belongs to the target org and is in scope before seeding it.
2. **Walk relationships with `assoc`.** After an enumeration has populated the graph, `assoc` queries the OAM (Open Asset Model) graph along *triples* to surface assets associated with what you already have (shared registrant, shared infrastructure, cert SANs). Its output is always JSON.

Keep this phase passive and conservative: the goal is to decide what is legitimately in scope, not to spray the org's whole footprint. Anything `assoc` surfaces that is **not** covered by the written scope is a lead to request an expansion for — not a target.

### Phase 2 — Passive enumeration (default, zero target traffic to speak of)

Default `enum` is passive: it pulls from data sources (certificate transparency logs, passive DNS, web archives, scraping, and dozens of APIs) and does **not** brute force or reach into the target's name servers.

```
amass enum -d example.com -v
```

Passive quality is a direct function of your **API keys**. With no keys you get free CT logs and a handful of open sources; with keys you get the paid passive-DNS and internet-scan providers that surface the long tail. Keys go in `datasources.yaml` (see Command recipes). This phase is the cheap, safe, high-yield core of Amass for bug bounty — run it first, run it fully-keyed, and re-run it on a schedule (Phase 6).

### Phase 3 — Active enrichment (opt-in, footprint-aware)

Only after passive is exhausted, and only if the program permits active recon, add active techniques one at a time so you can attribute any noise:

```
# brute force resolvable names (generates DNS traffic, not traffic to the target web app)
amass enum -d example.com -brute -w subdomains.txt -max-depth 1

# active grabs: zone-transfer attempts + TLS certificate name grabs against discovered hosts
amass enum -d example.com -active -p 443,80
```

`-active` is *"Attempt zone transfers and certificate name grabs"* — it connects directly to discovered hosts, so it is the first mode that is unambiguously visible to the target. `-brute` is *"Execute brute forcing after searches"* — it hammers resolvers, not the target's web tier, but a large wordlist with recursion is thousands of DNS queries. Gate both behind the program's "active scanning allowed?" answer. See *Pitfalls* for the footprint math.

### Phase 4 — Read and triage results

`enum` writes into the OAM graph database under `-dir` (file-based by default, or Postgres/BoltDB via `options.database`). You read it back with `subs`:

```
amass subs -d example.com -names        # just the discovered names → pipeline input
amass subs -d example.com -ip           # names with resolved IPv4/IPv6
amass subs -d example.com -summary      # ASN/netblock table — where assets cluster
amass subs -d example.com -show         # full results for the enumeration
```

Because the graph persists, `subs` reads *everything discovered to date*, not just the last run. Use `-summary` to spot netblocks the org clearly owns (a dense cluster under one ASN) versus one-off shared-hosting hits (deprioritise or confirm scope). For a picture you can attach to a report or share with a team, `viz` renders the graph (`-d3` → interactive HTML, `-dot` → Graphviz, `-gexf` → Gephi).

### Phase 5 — Handoff to the vulnerability pipeline

Amass output is an **input**, never a finding. Pipe names into liveness + templating:

```
amass subs -d example.com -names \
  | httpx -silent -title -tech-detect -status-code \
  | tee live.txt \
  | nuclei -silent -severity medium,high,critical
```

`subs -names` is the clean seam (one FQDN per line). Resolve/probe with httpx (or httprobe) *before* nuclei so you only template live hosts, and before you ever claim an asset exists in a report — data-source names go stale and un-probed names produce false positives.

### Phase 6 — Differential monitoring (the first-mover edge)

The persisted graph makes Amass a *change detector*, which is where the non-duplicate money is. Re-run `enum` on a cron, then ask `track` what is new since your last sweep:

```
# after a fresh enum populates the graph:
amass track -d example.com -since '11/01 00:00:00 2026 UTC'
```

`track` is *"Analyze OAM data to identify newly discovered assets"*; `-since` takes the Go reference layout `01/02 15:04:05 2006 MST`. A newly-appeared `staging-v2.example.com` the day it ships — before any other hunter's monthly scan catches it — is the freshest, least-reviewed surface on the target. Automate Phases 2→4→6 and alert on `track` deltas.

---

## Where the money is with amass

Amass finds surface; severity is always "whatever lives on the asset it found." The honest dupe picture: **everyone runs `amass enum -d` on day one**, so a raw subdomain list is near-worthless as a submission. Your edge is depth (keys other hunters don't have), scope expansion others miss, and differential timing.

| Finding class the asset enables | amass feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| Subdomain takeover (dangling CNAME on a discovered name) | `enum` data sources → `subs -names` → takeover check | High | High — first tool everyone runs |
| Net-new asset caught before other hunters | `track -since` on a persisted graph (Phase 6) | Varies — freshest code | **Low — your real edge** |
| Forgotten dev/staging/admin host | `-brute` + `-alts`, plus keyed passive sources | Med–High | Medium–High |
| In-scope assets under an ASN/CIDR others ignore | `scope.asns`/`cidrs` + `assoc` walks | Varies | Low–Medium |
| Non-prod API / panel on an unusual port | `-active` cert grabs + `-p` port set | Med–High | Medium |
| Internal name disclosure (zone transfer / NSEC walk) | `-active` (AXFR, NSEC) | Low (info) → High if chained | Low |
| CT-log-only / wildcard-hidden names | passive CT data sources (default) | Varies | High |

The pattern: the **low-dupe rows are the config-and-cadence rows** (keyed sources, scoped ASN seeds, `track` deltas), not the default `enum -d`. Compete on what you configured, not on the tool everyone shares.

---

## Command recipes

Each block is a real v5.1.x invocation with the low-volume / in-scope settings called out. Output shown is representative.

**Keyed passive enumeration — the safe default.** Passive is already the default mode; the value is in the keys.

```
amass enum -d example.com -config ~/.config/amass/config.yaml -v -oA recon/example
```
```
# representative console output
www.example.com
api.example.com
blog.example.com
...
# -oA writes recon/example.* ; the graph DB under -dir holds the authoritative data
```
`-v` shows which data sources answered; `-oA <prefix>` writes output files. No `-active`, no `-brute` → essentially invisible to the target's own infrastructure (traffic goes to data-source APIs and resolvers, not the target).

**`datasources.yaml` with API keys** — this file is where coverage comes from. `config.yaml` only points at it via `options.datasources`.

```yaml
# datasources.yaml  (referenced by options.datasources in config.yaml)
global_options:
  minimum_ttl: 1440            # minutes (1 day) — cache source answers, don't re-hammer APIs
datasources:
  - name: Shodan
    ttl: 10080                 # 7 days
    creds:
      account:
        apikey: YOUR_SHODAN_API_KEY
  - name: CIRCL
    creds:
      account:
        username: YOUR_USERNAME
        password: YOUR_PASSWORD
  - name: SecurityTrails
    creds:
      account:
        apikey: YOUR_SECURITYTRAILS_API_KEY
```
`ttl`/`minimum_ttl` throttle *you* against the providers — respect them so you don't burn a key or get rate-limited mid-engagement. Verify what Amass can see with `amass enum -list` (prints all available data sources).

**Scope-locked enumeration from a domain file** — the `-df` scope file keeps roots explicit and reviewable.

```
# scope.txt = one authorized root domain per line
amass enum -df scope.txt -config ~/.config/amass/config.yaml -rigid -v
```
`-df` is *"Path to a file providing root domain names"*; `-rigid` disables scope expansion. Keep `scope.txt` under version control as your audit trail of exactly what you were authorized to touch.

**Source include / exclude** — trim noisy or paid-but-exhausted sources, or restrict to a known-good set.

```
amass enum -d example.com -exclude Baidu,Ask            # drop noisy sources
amass enum -d example.com -include Crtsh,SecurityTrails # restrict to a trusted subset
```
`-include`/`-exclude` take comma-separated data source names (as printed by `-list`).

**Conservative brute force** — active DNS, minimal footprint.

```
amass enum -d example.com -brute -w subdomains.txt -max-depth 1 -min-for-recursive 2
```
`-max-depth 1` caps label depth; `-min-for-recursive 2` requires 2 labels seen before recursing (default 1) so you don't recurse into every single hit. Pick a *small, targeted* wordlist — a 100k-word list with recursion is a large DNS burst (see Pitfalls).

**Alteration / permutation pivot** — generate neighbour names from what you already found (`dev1`→`dev2`, `api-staging`→`api-stage`).

```
amass enum -d example.com -alts -aw alterations.txt
```
`-alts` enables *"generation of altered names"*; `-aw` supplies the alteration wordlist (`-awm`/`-wm` take hashcat-style masks). High yield once passive has seeded a naming convention; it is active resolution, so it counts as active traffic.

**Read results back for the pipeline.**

```
amass subs -d example.com -names | sort -u > names.txt      # clean seam for httpx/nuclei
amass subs -d example.com -summary                          # ASN/netblock clustering
```
```
# -summary representative output
ASN: 64496 - EXAMPLE-AS, US
    198.51.100.0/24    12    Subdomain Name(s)
```

**Association walk (JSON).**

```
amass assoc -d example.com -t1 "FQDN,example.com,cert_name" -dir ./graph
```
`assoc` queries the OAM graph along triples and always emits indented JSON — pipe it to `jq`. Use it to *justify scope*, not to expand into it.

**Differential since last sweep.**

```
amass track -d example.com -since '11/01 00:00:00 2026 UTC'
```
Timestamp format is `01/02 15:04:05 2006 MST` (Go reference time). Diff this against your previous `track` to alert on net-new assets.

**Resolver hygiene** — use your own trusted resolvers so results aren't poisoned and you aren't hammering one public resolver.

```
amass enum -d example.com -brute -w subdomains.txt -tr 1.1.1.1 -rf untrusted-resolvers.txt
```
`-tr` = trusted resolver IPs, `-r`/`-rf` = untrusted resolver IPs/file. Validate brute-force hits through trusted resolvers to cut wildcard/false-positive noise.

---

## Pitfalls, noise and scope discipline

**Default scope expansion is the ban risk.** Amass is built to discover assets you didn't list. On a bug bounty that is a liability: without `rigid_boundaries: true` / `-rigid`, it follows associations into acquired-company domains, CDN/shared-hosting neighbours, and netblocks the program never authorized — and now *you* sent traffic to an out-of-scope third party. Set rigid boundaries, seed only authorized roots/ASNs, and treat `blacklist` as a backstop. Every out-of-scope asset in your graph is a question to answer before you act on it.

**`-active` and `-brute` are the loud modes — know the math.** Passive default ≈ no target-visible traffic. `-active` connects to each discovered host for TLS cert grabs and attempts zone transfers (directly visible, logged by the target). `-brute` with a large recursive wordlist is thousands-to-millions of DNS lookups: a 100k-word list across even 200 discovered subdomains with recursion is a DNS burst that trips rate limits and IDS, and can hammer public resolvers you don't own. If the program says "no active scanning" or "no brute forcing," both flags are off the table — stay on passive. When allowed, cap with `-max-depth`, a small wordlist, `-min-for-recursive 2`, and your own resolvers.

**The v5 CLI is not the blog CLI.** Pasting `amass intel`, `amass db`, `amass enum -ip`, or `-dns-qps` from an old tutorial fails or silently misbehaves. Config typos are worse: the docs warn unsupported/misspelled keys *"may be silently ignored"* — a mistyped `rigid_boundaries` doesn't error, it just leaves scope expansion on. Diff your intended behavior against `-v` output.

**Raw Amass output is not a finding.** A subdomain list is where the work *starts*. Submitting "here are 400 subdomains" gets closed as non-actionable, and most of those names are duplicates of what every other hunter already enumerated. Validate (resolve + httpx), then find something *on* the asset.

**Stale and false-positive names.** Data-source names can be long-dead; brute/alt hits can be wildcard artifacts. Always re-resolve through trusted resolvers (`-tr`) and probe with httpx before reporting an asset as live. Reporting a dead name as a takeover is a fast way to lose triager trust.

**API-key and provider etiquette.** Respect `ttl`/`minimum_ttl` so you cache answers instead of re-querying; a burned SecurityTrails/Shodan key mid-engagement costs you the long tail. Some sources have their own ToS about automated use.

**Runaway enumerations.** A broad org with brute + alterations and no `-timeout` can run for hours and bloat the graph DB. `-timeout` is *"Minutes to run without progress before terminating (Default: 30)"* — keep it sane, and scope tightly.

---

## Reporting evidence

Amass findings are rarely submitted as "Amass found X"; Amass evidence *supports* a finding (takeover, exposed panel, net-new surface) and, just as importantly, *proves you stayed in scope*. Capture:

- **The exact command line**, including the scope-safety flags (`-df scope.txt`, `-rigid`, no `-active`/`-brute` unless authorized). This demonstrates discipline to the triager as much as it reproduces the result.
- **The scope file / `config.yaml` scope block** you ran with — your own record of what you were authorized to touch.
- **The specific asset**, from `subs`: `amass subs -d example.com -names` / `-ip` output showing the FQDN and its resolution, with the enumeration timestamp.
- **For a subdomain takeover**: the dangling chain (`dig CNAME <name>` → unclaimed provider) plus your controlled-claim PoC — not just the Amass line.
- **For a scope-justification**: the relevant `assoc` JSON walk tying the asset back to an in-scope root/ASN, so there's no argument about whether it was in scope.
- **For a first-mover / net-new claim**: the `track -since` output and the date, showing the asset appeared after your prior sweep.
- **The OAM graph DB directory (`-dir`)** retained as the audit trail of the whole engagement.

Keep the proof minimal and in-scope: show the one asset and what makes it a finding, never a full graph dump, and clean up any test artifacts.

---

## References

- OWASP Amass — official documentation (configuration): https://owasp-amass.github.io/docs/configuration
- OWASP Amass — data sources / API credentials: https://owasp-amass.github.io/docs/configuration/data_sources
- OWASP Amass — repository (v5, module `github.com/owasp-amass/amass/v5`): https://github.com/owasp-amass/amass
- v5 subcommand dispatch (`cmd/amass/main.go`, `main` branch): https://github.com/owasp-amass/amass/blob/main/cmd/amass/main.go
- v5 `enum` CLI flags (`internal/enum/cli.go`): https://github.com/owasp-amass/amass/blob/main/internal/enum/cli.go
- v5 `subs` CLI flags (`internal/subs/cli.go`): https://github.com/owasp-amass/amass/blob/main/internal/subs/cli.go
- v5 `track` CLI flags (`internal/track/cli.go`): https://github.com/owasp-amass/amass/blob/main/internal/track/cli.go
- v5 `assoc` CLI flags (`internal/assoc/cli.go`): https://github.com/owasp-amass/amass/blob/main/internal/assoc/cli.go
- v5 `viz` CLI flags (`internal/viz/cli.go`): https://github.com/owasp-amass/amass/blob/main/internal/viz/cli.go
- OWASP Amass — users' guide (background, pre-v5 reference): https://github.com/owasp-amass/amass/blob/master/doc/user_guide.md

*Research supplement added 2026-11-15, grounded in the OWASP Amass v5 source (`cmd/amass/main.go` and `internal/{enum,subs,track,assoc,viz,amass_engine}/cli.go` on the `main` branch, module `github.com/owasp-amass/amass/v5`) and the official configuration/data-source docs (owasp-amass.github.io/docs); corpus evidence for the packaged wordlist path remains limited (older references point other tools at `/usr/share/amass/wordlists/...`), and the pre-v5 "Typical use cases" above are preserved as-is — the Version note reconciles them with the v5.1.x CLI (model: claude-opus-4-8).*
