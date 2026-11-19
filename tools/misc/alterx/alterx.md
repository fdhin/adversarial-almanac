---
name: "alterx"
categories:
  - blackarch-misc
version: "v0.1.0.r17.g3329d23"
homepage: "https://github.com/projectdiscovery/alterx"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-24"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/alterx"
github-stars: 1018
github-pushed: "2026-11-06"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 27
gh-activity-checked: "2026-11-09"
---

# alterx

> Fast, pattern-driven subdomain permutation wordlist generator using a small DSL.

*No corpus evidence — derived from upstream documentation.*

## What it's for

alterx (by projectdiscovery) generates subdomain wordlists by applying permutation patterns to subdomains you already know — typically the output of passive enumeration. Patterns are templates mixing input-derived variables (`{{sub}}`, `{{suffix}}`, `{{tld}}`, `{{etld}}`, `{{sld}}`, `{{root}}`, `{{sub1}}`) with payload words (`{{word}}`), so the generated names resemble how the target actually names hosts (`api-prod`, `prod-api`, `api.dev`) instead of a hardcoded mega-list. It only produces names: resolution and validation are left to DNS tools such as dnsx, normally fed straight through a pipe. Input comes from stdin, a file, or a comma-separated list.

## Typical use cases

### Permute known subdomains and resolve them in one pipeline

Feed a passive-enumeration result list in and hand the permutations straight to a resolver.

```
cat known-subs.txt | alterx | dnsx -silent
```

Outcome: prints `[INF] Generated N permutations in Xs` and streams the generated names; dnsx resolves them and `-silent` keeps only the ones that answer, giving the next-round target list.

### Enrich the word list from the input itself

Let alterx harvest extra permutation words from the subdomain names you already have.

```
cat known-subs.txt | alterx -enrich
```

Outcome: words extracted from the input (project, environment, team names visible in existing subdomains) join the default payloads, producing target-aware permutations — typically an order of magnitude more names, so estimate or cap the output before resolving.

### Generate with a custom pattern

Override the default pattern set at run time.

```
alterx -list known-subs.txt -p '{{sub}}-{{word}}.{{suffix}}'
```

Outcome: only permutations matching the given template are generated (e.g. `api-prod.example.com` for input `api.example.com`); repeat `-p` to supply several patterns, or point `-ac` at a custom permutation config YAML for a reusable set.

### Supply your own payload words

Define what `{{word}}` (or any new variable) expands to.

```
alterx -list known-subs.txt -p '{{word}}-{{year}}.{{suffix}}' -pp word=keywords.txt -pp year=2026
```

Outcome: `-pp key=value` binds a variable to a wordlist file or a literal, so generated names like `prod-2026.example.com` draw on your own vocabulary — useful when the target's naming convention is known from other reconnaissance.

### Estimate the permutation count before generating

Check how large a run will be without materializing it.

```
alterx -list known-subs.txt -enrich -es
```

Outcome: reports the number of permutations the current options would produce (`-es`, estimate) without writing them out — use it to decide whether to add `-limit N` or trim patterns before committing to a DNS brute-force run.

## Bug bounty workflow with alterx

alterx is not a scanner and finds no vulnerabilities on its own. It is a *candidate generator* that sits in the middle of a subdomain-discovery pipeline: passive enumeration gives you the hosts the target advertises, alterx guesses the hosts they *forgot to hide*, and a resolver tells you which of those guesses exist. The value is structural — permutations reconstruct an organisation's naming convention (`api` → `api-dev`, `api-staging`, `api-internal`, `api2`), and the hosts that match a convention but never made it into DNS records you can find passively are disproportionately the neglected ones: staging boxes, legacy deployments, un-monitored admin panels, dangling CNAMEs left after a migration. Those are where takeovers and weak-auth findings live. The whole workflow below is built around one discipline problem: **alterx multiplies your input, so every unmanaged step downstream becomes a DNS flood.** Gate it, estimate it, cap it.

The frontmatter pins this vault's build at `v0.1.0.r17.g3329d23`; the flags and defaults below are verified against the current upstream README and `docs.projectdiscovery.io` (see References). Where a flag is adjacent-tool (dnsx, shuffledns, puredns), it is called out as such.

### Phase 0 — Scope and authorization gate

Before generating anything, answer three program questions, because alterx's output defines the blast radius of everything after it:

- **Is the wildcard (`*.example.com`) in scope?** Permutation hunting only makes sense against a wildcard or an explicit "all subdomains" scope. If the program lists individual hosts only, every new subdomain you resolve is out of scope and un-reportable — and the resolution traffic against the target's authoritative DNS is unauthorized.
- **What resolution volume is permitted?** alterx generates names locally (no traffic to the target), but the *resolver* you pipe into (dnsx/shuffledns/puredns) sends one or more DNS queries per name. A default run against a handful of seeds is thousands of queries; an `-enrich` run is hundreds of thousands. Confirm the program tolerates active DNS brute force and at what rate.
- **Whose resolvers?** Point resolution at public resolvers or your own, never at the target's authoritative nameservers as a stress source. Use a vetted trusted-resolver list so wildcard DNS and poisoned resolvers don't manufacture false positives you then "report".

### Phase 1 — Build the seed set (passive first)

alterx is only as good as its input: it permutes *around* known names, so garbage or thin seeds yield garbage permutations. Assemble the seed list from passive sources first — this costs the target nothing and gives alterx the real naming vocabulary to mutate.

```
subfinder -d example.com -silent -all > seeds.txt
# optionally add chaos, amass passive, crt.sh — dedupe into one file
sort -u seeds.txt -o seeds.txt
```

Feed only *resolving, in-scope* seeds. A seed set of 50–500 real subdomains is the sweet spot: enough convention signal for `-enrich` to mine, small enough to keep the multiplier sane.

### Phase 2 — Estimate before you generate

This is the single most important habit with alterx. `-es`/`-estimate` reports how many permutations the current options would produce **without materialising or resolving them**:

```
alterx -list seeds.txt -es
alterx -list seeds.txt -enrich -es
```

The second number is the one that bites: upstream's own README benchmark shows a single root expanding to **8,312** names with the default patterns and **662,010** with `-enrich` — an ~80× jump from one flag. Read the estimate as "DNS queries I am about to send" and decide your cap *before* you ever pipe into a resolver.

### Phase 3 — Generate (patterns and payloads)

Default run uses the shipped pattern/payload config (`$HOME/.config/alterx/permutation_v0.0.1.yaml`): 8 active patterns over a 110-word payload, a 24-entry `number` list, and a 6-entry `region` list. Tighten or widen deliberately:

```
# Default patterns, capped output, written to file (no resolution yet)
alterx -list seeds.txt -limit 20000 -o perms.txt

# Convention-targeted: only the two naming shapes you actually observed
alterx -list seeds.txt -p '{{sub}}-{{word}}.{{suffix}}' -p '{{word}}.{{sub}}.{{suffix}}' -o perms.txt

# Your own vocabulary (e.g. words harvested from JS, docs, the target's own hosts)
alterx -list seeds.txt -p '{{word}}.{{suffix}}' -pp word=mywords.txt -o perms.txt
```

Prefer a *narrow pattern + curated word list* over `-enrich` with full defaults when the program is rate-sensitive: it generates an order of magnitude fewer names while still covering the target's real convention.

### Phase 4 — Resolve safely

alterx streams names to stdout; the resolver is where traffic and discipline matter. Cap the rate, use trusted resolvers, and keep concurrency modest.

```
# dnsx: rate-limited, silent, A-record resolution of the capped list
alterx -list seeds.txt -limit 20000 | dnsx -silent -rl 100 -t 50 -o live.txt

# shuffledns/puredns (massdns-backed) for larger sets, with a trusted-resolver file
alterx -list seeds.txt | shuffledns -d example.com -r resolvers-trusted.txt -mode resolve -o live.txt
```

`-rl` (dnsx rate limit, queries/sec) and a trusted-resolver list are the two settings that keep this from looking like a DNS flood and from producing wildcard false positives. Resolve, then stop — do not re-run the same set repeatedly.

### Phase 5 — Probe and triage the live hosts

Only resolved names matter. Probe them for live HTTP services and metadata, then triage:

```
httpx -l live.txt -silent -sc -title -tech-detect -o http-live.txt
```

Sort the survivors by *interestingness*, not count: hosts whose names contain `dev`, `staging`, `internal`, `legacy`, `admin`, `vpn`, `backup`, `test`, `uat` (all in alterx's default word list) are the forgotten-asset candidates. A 200 on `admin-staging.example.com` with a default login, or a dangling CNAME on `legacy-app.example.com`, is the finding — alterx just surfaced the host.

### Phase 6 — Iterate (bounded recursion)

Found names feed the next round: resolved subdomains become new seeds, and alterx permutes the *newly discovered* convention (e.g. finding `api-dev` teaches it to try `api-dev2`, `api-dev-internal`). Do this at most one or two levels deep, re-estimating each round — recursion is where unbounded explosions happen.

```
# one controlled recursion step
alterx -list live.txt -es                       # check the new multiplier first
alterx -list live.txt -limit 10000 | dnsx -silent -rl 100 | anew live.txt
```

## Where the money is with alterx

alterx doesn't produce the finding; the *resolved permutation* leads to it. This table maps the asset class a permutation surfaces to the alterx feature that reaches it and the realistic payout once you've validated the host with another tool.

| Finding class (on the surfaced host) | alterx feature/command that reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| Subdomain takeover (dangling CNAME on a forgotten host) | Default patterns + resolve; look for `NXDOMAIN`/service-fingerprint on `{{word}}.{{suffix}}` and legacy names | High–Critical | Medium — popular roots are heavily permuted by others |
| Staging/dev host with weak or no auth | `dev`/`staging`/`stage`/`uat`/`qa`/`test` default words via `{{sub}}-{{word}}`, `{{word}}.{{sub}}` | High | Medium |
| Internal/admin panel exposed publicly | `admin`/`internal`/`corp`/`dashboard`/`manager` words; `{{word}}.{{suffix}}` replace pattern | High | Low–Medium |
| Numbered/legacy duplicate (`api2`, `www1`, old prod) | `{{sub}}{{number}}.{{suffix}}` + `legacy`/`backup` words | Medium–High | Low |
| Region/cloud-shard host missed by passive | `{{region}}.{{sub}}.{{suffix}}` region payload | Medium | Low |
| Net-new asset found before the crowd | `-enrich` mines the target's own tokens → convention-specific names others' static lists miss | Varies (amplifier) | **Lowest** — bespoke names are rarely duplicated |
| Pre-disclosure asset inventory for a broader chain | Recursion (Phase 6): each resolved host seeds the next permutation round | Info → feeds other bugs | Low |

Dupe-risk reality: on a well-known root, default-pattern permutations are the *first* thing every hunter runs, so popular names are picked clean. The durable edge is `-enrich` + a curated, target-specific word list (Phase 3) — names derived from the organisation's own vocabulary are the ones generic wordlists never contain.

## Command recipes

Each block is annotated with the low-volume setting that keeps it program-safe. alterx itself sends no traffic to the target; the discipline is in capping what you hand the resolver.

```
# 1) ALWAYS estimate first — no output, no resolution, just the multiplier
alterx -list seeds.txt -enrich -es
# -> [INF] prints the permutation count; treat it as "DNS queries about to be sent"
```

```
# 2) Generate to a file with a hard cap; inspect before resolving
alterx -list seeds.txt -limit 20000 -o perms.txt
# -limit 20000 stops generation at 20k names regardless of pattern explosion
# -o writes to disk so you can review/sample before any query leaves your box
```

```
# 3) Convention-locked generation (fewest names, highest signal)
alterx -list seeds.txt \
  -p '{{sub}}-{{word}}.{{suffix}}' \
  -p '{{word}}-{{sub}}.{{suffix}}' \
  -pp word=observed-words.txt -o perms.txt
# two patterns + your own word file = ~10x fewer names than default, all on-convention
```

```
# 4) Full pipeline, rate-capped end to end
alterx -list seeds.txt -limit 50000 | dnsx -silent -rl 150 -t 50 | \
  httpx -silent -sc -title -o http-live.txt
# dnsx -rl 150 caps DNS at 150 q/s; -t 50 holds concurrency down
# httpx probes only what resolved — the live set, not the guess set
```

```
# 5) Large set via massdns-backed resolver with trusted resolvers
alterx -list seeds.txt | \
  puredns resolve --resolvers resolvers-trusted.txt --rate-limit 300 -q > live.txt
# trusted resolvers prevent wildcard/poisoned false positives at scale
```

```
# 6) Custom variable beyond the built-ins (year, region, team, etc.)
alterx -list seeds.txt -p '{{word}}-{{year}}.{{suffix}}' \
  -pp word=keywords.txt -pp year=2026 -o perms.txt
# -pp key=value binds any {{var}} to a file or a literal; literals need no file
```

```
# 7) Cap export *size* rather than count when names vary in length
alterx -list seeds.txt -enrich -ms 5mb -o perms.txt
# -ms/-max-size bounds the output file (kb/mb/gb/tb) as a second safety valve
```

Flag reference actually used above (verified against upstream help): `-l/-list`, `-p/-pattern` (repeatable), `-pp/-payload` (`key=value`, repeatable), `-es/-estimate`, `-o/-output`, `-ms/-max-size`, `-en/-enrich`, `-ac` (custom permutation config), `-limit`, `-silent`, `-v/-verbose`, `-config`, `-up/-update`, `-duc/-disable-update-check`, `-version`. There is **no** recursion-depth flag — bounded recursion (Phase 6) is something you orchestrate yourself by re-feeding output, not a built-in option. The only "max" flag is `-ms` (export size); it does not limit the permutation count — use `-limit` for that.

## Pitfalls, noise and scope discipline

**The `-enrich` explosion is the classic self-inflicted wound.** One flag took upstream's benchmark from 8,312 to 662,010 names. Piped straight into a resolver with no cap, that is hundreds of thousands of DNS queries — enough to trip a program's abuse monitoring, exhaust a resolver, and bury the few real hosts in noise. **Always `-es` before `-enrich`, always pair it with `-limit` or a narrowed `-p`.**

**alterx is harmless; the resolver is the loud part.** Reviewers sometimes assume alterx "scans" the target — it does not; generation is entirely local. The traffic and the scope exposure come from dnsx/shuffledns/puredns. Put your rate limits there (`dnsx -rl`, `puredns --rate-limit`), and use a *trusted* resolver list so wildcard DNS doesn't report thousands of phantom hosts as live.

**Wildcard DNS manufactures false positives.** Many targets answer every `*.example.com` query with the same IP. Without wildcard filtering (puredns/shuffledns do this; raw dnsx needs care), alterx's guesses all "resolve" and you report hosts that don't exist. Validate with HTTP probing and distinct-content checks before believing a resolution.

**Out-of-scope subdomains are a reporting trap.** A wildcard scope is not infinite scope: programs frequently carve out acquired brands, third-party-hosted subdomains, and specific prefixes (`*.corp`, `*.internal` reachable only via VPN). A resolving host is not automatically an in-scope, reportable one — re-check each surfaced host against the written scope before touching it.

**Resolving ≠ a finding.** Discovering `staging.example.com` exists is reconnaissance, not a vulnerability. Reports that are just "I found these 40 subdomains with alterx" are closed as informational/N/A. The finding is what the host *is* (takeover-able, unauthenticated admin, leaked data) — alterx's output is the first step, not the deliverable.

**Thin or dirty seeds waste the run.** Permuting a seed list full of out-of-scope or dead names just multiplies junk. Feed resolving, in-scope, passively-found seeds (Phase 1), and `-enrich` mines *their* tokens — garbage in, garbage ×80 out.

**Don't hammer the target's own nameservers.** Direct your resolver at public/trusted resolvers, never at the authority as a stress source. High-volume brute force against authoritative DNS can read as a DoS attempt and get you removed from the program.

## Reporting evidence

alterx is a recon-stage tool, so its output belongs in the *methodology* section of a report, not as the finding itself. Capture:

- **The exact generation command**, including the pattern(s), payload file(s), and any `-enrich`/`-limit`/`-ac` used — so a triager can reproduce the candidate list deterministically. Note which alterx build/config (`permutation_v0.0.1.yaml` vs. a custom `-ac` file) produced it; pin the version with `alterx -version`.
- **The `-es` estimate and the applied cap**, demonstrating you controlled volume (e.g. "estimated 410k permutations with `-enrich`; capped resolution at `-limit 20000` and `dnsx -rl 100`"). This pre-empts any "did you flood us?" question.
- **The resolution evidence for the surfaced host** — the `dnsx`/`puredns` line showing the name resolving (A/CNAME record), plus the trusted-resolver list used, so the host can't be dismissed as a wildcard artifact.
- **The actual finding on that host**, captured with the appropriate tool: the `httpx` line (status, title, tech), the takeover fingerprint, the HTTP request/response pair for an exposed panel, screenshots. This is the payload of the report.
- **Scope justification**: a one-line note that the surfaced subdomain falls under the program's wildcard/all-subdomains scope and is not in an excluded carve-out.

Keep raw, oversized permutation lists out of the report body — attach a sample and the command, not 600k lines. The report's value is the validated host and its vulnerability; alterx is the cited step that found it.

## References

- alterx GitHub repository and README (usage, flags, DSL variables, examples) — https://github.com/projectdiscovery/alterx
- alterx README (raw) — https://raw.githubusercontent.com/projectdiscovery/alterx/main/README.md
- Default permutation config (`permutations.yaml`: 8 active patterns, `word`/`region`/`number` payloads) — https://raw.githubusercontent.com/projectdiscovery/alterx/main/permutations.yaml
- ProjectDiscovery docs — alterx overview — https://docs.projectdiscovery.io/tools/alterx/overview
- ProjectDiscovery docs — alterx usage (flag list) — https://docs.projectdiscovery.io/tools/alterx/usage
- ProjectDiscovery docs — alterx running (DSL variables, benchmark examples) — https://docs.projectdiscovery.io/tools/alterx/running
- dnsx (resolver in the pipeline) — https://github.com/projectdiscovery/dnsx
- shuffledns (massdns-backed resolve/brute) — https://github.com/projectdiscovery/shuffledns
- puredns (wildcard-filtering mass resolver) — https://github.com/d3mondev/puredns
- Introducing alterx (ProjectDiscovery blog) — https://blog.projectdiscovery.io/introducing-alterx-simplifying-active-subdomain-enumeration-with-patterns/

*Research supplement added 2026-11-16, grounded in the upstream alterx README, `permutations.yaml`, and docs.projectdiscovery.io (overview/usage/running) as cited above (model: claude-opus-4-8).*
