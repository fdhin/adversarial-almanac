---
name: "shuffledns"
categories:
  - blackarch-webapp
version: "851.bc31821"
homepage: "https://github.com/projectdiscovery/shuffledns.git"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-21"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/shuffledns"
github-stars: 1672
github-pushed: "2026-11-04"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 29
gh-activity-checked: "2026-11-09"
---

# shuffledns

> massdns wrapper for fast subdomain bruteforcing and resolving with automatic wildcard stripping.

## What it's for

shuffledns (ProjectDiscovery) wraps massdns to make it practical: it bruteforces or resolves
subdomains at massdns speed, then cleans the result by detecting and filtering DNS wildcards —
including multi-level ones — with a small number of extra queries, so a wildcarded domain does
not turn every guess into a false hit. Input and output are file- and pipe-friendly for pipeline
use. It is not a passive source: it needs the `massdns` binary on PATH (or pointed to with `-m`)
and a resolver list. Only one operation (resolve or bruteforce) runs per invocation.

## Typical use cases

### Bruteforce subdomains with a wordlist

```
shuffledns -d example.com -w wordlist.txt -r resolvers.txt -mode bruteforce
```

Outcome: every `word.example.com` candidate is resolved through massdns using the supplied
resolvers; valid subdomains are printed (wildcard-inflated answers filtered out), and the domain
is effectively enumerated in minutes rather than hours.

### Resolve an existing list of subdomains

```
shuffledns -d example.com -l subdomains.txt -r resolvers.txt -mode resolve
```

Outcome: each candidate in the list is mass-resolved; only subdomains that actually resolve are
returned, deduplicated — useful for validating passive-enum output before further tooling.

### Resolve a pipeline of discovered names

```
subfinder -d example.com | shuffledns -d example.com -r resolvers.txt -mode resolve
```

Outcome: passive discoveries on stdin are resolved and filtered in one step, and the surviving
live subdomains flow to stdout for the next stage of a recon pipeline.

### Keep wildcard IPs for review

```
shuffledns -d example.com -w wordlist.txt -r resolvers.txt -mode bruteforce -wo wildcard-ips.txt
```

Outcome: the IPs identified as wildcard infrastructure are dumped to `wildcard-ips.txt` so the
filtering decision can be audited instead of trusted blindly.

## Bug bounty workflow with shuffledns

shuffledns is a DNS *multiplier*, not a discovery source: it takes names you already
have (resolve mode) or names you invent from a wordlist (bruteforce mode) and tells you
which ones answer, with wildcard noise stripped. That makes it the resolver stage of a
recon pipeline, and the single most important thing to understand before running it is
that **bruteforce mode is active, high-volume recon**: it fires one DNS query per
wordlist entry per retry, through massdns, at a default concurrency of 10,000 lookups
(`-t`). A 100k wordlist with the default `-retries 5` is up to half a million queries
against the target's authoritative nameservers and whatever public resolvers you loaded.
Resolve mode is far quieter — one query per known name — but it is still active DNS
traffic that leaves your guesses in third-party resolver logs. Treat the whole tool as
an activity you turn *up* only after passive sources are exhausted and the program
permits active subdomain enumeration.

The phases below go from program-safe (passive + resolve) to loud (bruteforce), with a
hard gate at each escalation. Do not skip to Phase 3 on a fresh target.

### Phase 0 — Authorization and scope gate

Before a single query leaves your box, confirm three things in writing against the
program's rules of engagement:

- **Which apex domains are in scope.** shuffledns bruteforce is bound to the `-d` domain
  you give it; resolve mode filters discovered names against `-d` for wildcard handling.
  Only ever pass an in-scope apex. `*.example.com` in scope does **not** authorize
  bruteforcing `example-cdn.net` just because a CNAME points there.
- **Whether active subdomain enumeration / DNS bruteforce is allowed.** Many programs
  explicitly restrict high-volume automated scanning or rate-limit active recon.
  Bruteforce mode is exactly the kind of traffic those clauses mean. If the program says
  "no automated scanning," resolve mode on passively-discovered names is usually fine;
  bruteforce is not — ask.
- **Wildcard DNS policy.** If the apex is a wildcard (`*.example.com` resolves anything),
  bruteforce produces enormous query volume for near-zero signal. Know this before you
  start (Phase 4) rather than after you have sent 500k queries.

### Phase 1 — Build and validate the resolver set (this decides your result quality)

shuffledns is only as good as `resolvers.txt`. massdns resolves through whatever
resolvers you hand it, and the upstream massdns README is blunt about the risk: the
bundled list is "currently outdated with a large share of resolvers being dysfunctional,"
and "the provided resolvers are not guaranteed to be trustworthy." A stale or poisoned
resolver returns wrong or injected answers, which become **false-positive subdomains** —
and false positives are how hunters file invalid, duplicate-looking reports that get them
throttled by triage. Never run shuffledns against a resolver list you did not validate.

Two maintained options, in order of effort:

```
# Option A: pull a continuously-validated public list (fast, good enough to start)
# trickest/resolvers is rebuilt on a constant schedule and validated with dnsvalidator
wget -q https://raw.githubusercontent.com/trickest/resolvers/main/resolvers.txt -O resolvers.txt
wget -q https://raw.githubusercontent.com/trickest/resolvers/main/resolvers-trusted.txt -O trusted.txt
```

```
# Option B: build your own validated list from a public nameserver feed
# dnsvalidator baselines each candidate against 1.1.1.1 / 8.8.8.8 / 9.9.9.9 and
# drops any resolver that answers random subdomains instead of NXDOMAIN (spoofers)
dnsvalidator -tL https://publicdns.info/nameservers.txt -threads 20 -o resolvers.txt
```

Keep two lists and use both: a large `resolvers.txt` of hundreds-to-thousands of public
resolvers for throughput, and a tiny `trusted.txt` of operator-run resolvers you trust
(Cloudflare `1.1.1.1`, Google `8.8.8.8`, Quad9 `9.9.9.9`). shuffledns exposes the second
as a first-class input: `-tr/-trusted-resolver`. The pattern is "resolve wide with the
big list, re-validate survivors against trusted resolvers" — this is the single most
effective false-positive filter you have, and it mirrors the massdns README's own advice
to "re-run against trusted resolvers to remove false positives."

> Note on dnsvalidator volume: its own README warns that a high `-threads` count "can
> make it look like you are attempting to attack DNS servers, resulting in network level
> DNS blocks from your ISP." Keep it modest; this is validation, not the main scan.

### Phase 2 — Passive first, resolve-only (the low-footprint default)

The program-safe default is to let a *passive* source do the discovery and use shuffledns
only to prune the list down to names that actually resolve. This sends one query per
discovered name — orders of magnitude less traffic than bruteforce — and produces the
live-host list every later phase consumes.

```
subfinder -d example.com -silent | shuffledns -d example.com -r resolvers.txt -mode resolve -silent -o live.txt
```

Outcome: passive discoveries stream in on stdin, massdns resolves each, wildcard-inflated
answers are filtered against `example.com`, and only names that genuinely resolve land in
`live.txt`. This is the stage you can run on almost any program that permits active recon
at all, because the query count is bounded by the passive result set, not by a wordlist.

Snapshot `live.txt` per engagement and diff across visits — a newly-resolving subdomain is
the freshest, least-reviewed surface on the target and your best shot at escaping the
subdomain-enumeration dupe swamp (see "Where the money is").

### Phase 3 — Wordlist bruteforce (loud; gate on Phase 0)

Only when passive + resolve is exhausted, the program allows active enumeration, and the
apex is *not* a wildcard (Phase 4), escalate to bruteforce. This is the tool's loudest
mode and its highest false-positive surface.

```
shuffledns -d example.com -w wordlist.txt -r resolvers.txt -mode bruteforce \
  -wo wildcard-ips.txt -o brute-hits.txt -silent
```

Throttle it for bounty use. The default `-t 10000` is massdns's concurrent-lookup count
and is tuned for throughput, not courtesy. Lower it, and pass massdns its own brakes
through `-mcmd` (extra massdns args, e.g. an inter-resolve interval):

```
# low-and-slow: cut concurrency, add a wait interval between repeated resolves of a name
shuffledns -d example.com -w wordlist.txt -r resolvers.txt -mode bruteforce \
  -t 1000 -mcmd "-i 1000" -o brute-hits.txt -silent
```

Choose wordlist size deliberately: a focused 10k–20k list (commonspeak2, a trimmed
SecLists DNS list) finds the common names without the half-million-query tail of a
giant list. Bigger wordlists mostly buy more wildcard noise and more resolver load, not
more real hosts.

### Phase 4 — Wildcard adjudication and trusted re-validation

shuffledns's headline feature is wildcard stripping: it counts how many candidate names
point at each IP, and when a count crosses a small internal threshold it runs wildcard
checks at every DNS level for that IP, so a `*.example.com` that answers everything does
not turn every guess into a "hit." The wildcard filter only works with `-d` input, so
always supply the apex.

Two levers matter for trusting the result:

- `-wo/-wildcard-output` dumps the IPs shuffledns classified as wildcard infrastructure
  so you can audit the filtering decision instead of trusting it blindly. Always set it
  on a bruteforce run.
- `-sw/-strict-wildcard` runs a wildcard check on *all* found subdomains (not just those
  over the threshold). Slower and more queries, but on a wildcard-heavy target it is the
  difference between a clean list and a list full of phantom hosts.

Then re-validate survivors against trusted resolvers to kill resolver-level false
positives:

```
shuffledns -d example.com -l brute-hits.txt -r trusted.txt -mode resolve -o confirmed.txt -silent
```

A name that survives both a large-list resolve and a trusted-resolver re-resolve is a
real, live host. That two-pass discipline is what separates a reportable asset from a
poisoned-resolver artifact.

### Phase 5 — Triage resolved hosts into findings

shuffledns itself reports no vulnerability — it reports *hosts*. The finding is downstream.
Feed `confirmed.txt` into the checks that actually pay:

```
# resolved host -> live HTTP service -> fingerprint (the surface-expansion pivot)
cat confirmed.txt | httpx -silent -title -tech-detect -status-code -o web-live.txt

# resolved host with a dangling CNAME -> subdomain takeover candidate (the direct finding)
cat confirmed.txt | dnsx -silent -cname -resp | grep -iE 's3|azure|github\.io|herokuapp|fastly|unbouncepages|cargocollective'
```

A subdomain whose CNAME points at a deprovisioned third-party service is a classic
takeover — the one finding class shuffledns feeds *directly*. Everything else (new login
panels, staging/dev hosts, forgotten admin interfaces, API hosts) is surface for the rest
of your methodology.

---

## Where the money is with shuffledns

shuffledns produces an asset list; its "value" is really the value of what the list feeds.
Be honest about dupe risk: `subfinder | shuffledns` is the most-run pipeline in bug
bounty, so a raw subdomain list on a mature program is close to worthless and routinely
rejected as a non-issue. The money is in (a) the one finding class it feeds directly —
takeover — and (b) being first to a *newly* appeared or bruteforce-only host.

| Finding class | What shuffledns contributes | Typical severity | Dupe risk |
|---|---|---|---|
| Subdomain takeover (dangling CNAME) | Resolved host list → dnsx CNAME check finds names pointing at deprovisioned services | High–Critical | Medium — first-finder wins; stale assets get re-checked constantly |
| Forgotten dev/staging/admin host | Bruteforce surfaces `dev.`, `staging.`, `admin.`, `internal.` names absent from passive sources | Medium–High (as a pivot to the real bug) | Medium–High |
| New surface vs. a baseline | Diffing `live.txt` across visits catches freshly-shipped hosts before review | depends on what's on it | Low if you're early |
| Internal-only name disclosure | Names resolving to RFC1918 / internal IPs leak architecture | Low–Info | High |
| Wildcard/misconfig intel | `-wo` wildcard-IP dump shows catch-all infra and sometimes mis-scoped ranges | Info | High |
| Bare subdomain list (no pivot) | The default output, unenriched | Info / often N/A | **Very high — usually rejected** |

The reusable rule: never report a shuffledns list. Report the *takeover*, the *exposed
admin panel*, the *API host that leaks data* — and use the resolved list only as the
evidence trail showing how you reached it.

---

## Command recipes

Annotated, low-volume-first. Representative output shown; your names will differ.

```
# Resolve-only, passive-fed — the safe default (Phase 2)
subfinder -d example.com -silent | shuffledns -d example.com -r resolvers.txt -mode resolve -silent
# -> api.example.com
#    cdn.example.com
#    mail.example.com
# One query per discovered name; wildcard-filtered against example.com; dedup'd to stdout.
```

```
# Resolve a saved candidate list with machine-readable output
shuffledns -d example.com -l candidates.txt -r resolvers.txt -mode resolve -j -o resolved.ndjson -silent
# -> {"host":"api.example.com","ip":["198.51.100.10"]}
# -j emits ndjson (host + resolved IPs) for scripting; -o also writes the file.
```

```
# Bruteforce, throttled, wildcard IPs captured (Phase 3 — active, gated)
shuffledns -d example.com -w dns-10k.txt -r resolvers.txt -mode bruteforce \
  -t 1000 -mcmd "-i 1000" -wo wildcard-ips.txt -o brute-hits.txt -silent
# -t 1000: massdns concurrency down from the 10000 default.
# -mcmd "-i 1000": pass massdns a 1000ms interval between repeated resolves of a name.
# -wo: dump IPs judged to be wildcard infra, for audit.
```

```
# Strict wildcard mode on a wildcard-heavy target (Phase 4)
shuffledns -d example.com -w dns-10k.txt -r resolvers.txt -mode bruteforce \
  -sw -wo wildcard-ips.txt -o brute-hits.txt -silent
# -sw/-strict-wildcard: wildcard-check every found name, not just high-count IPs.
# Slower and more queries, but kills phantom hosts on catch-all domains.
```

```
# Trusted re-validation pass — the false-positive killer (Phase 4)
shuffledns -d example.com -l brute-hits.txt -r trusted.txt -mode resolve -o confirmed.txt -silent
# Re-resolve survivors through a tiny trusted list (1.1.1.1 / 8.8.8.8 / 9.9.9.9).
# Alternatively hand shuffledns both lists at once via -tr/-trusted-resolver on the brute run.
```

```
# Point shuffledns at a non-standard massdns path
shuffledns -d example.com -l candidates.txt -r resolvers.txt -mode resolve -m /opt/massdns/bin/massdns -silent
# Auto-detected at /usr/bin/massdns or /usr/local/bin/massdns; -m/-massdns overrides.
```

Flag reference actually used above (all verified against the current README help):

| Flag | Meaning |
|---|---|
| `-d/-domain` | target apex; required for wildcard filtering |
| `-l/-list` | file of names to resolve |
| `-w/-wordlist` | wordlist for bruteforce |
| `-r/-resolver` | resolver list (the quality lever) |
| `-tr/-trusted-resolver` | trusted-resolver list for re-validation |
| `-mode` | `bruteforce`, `resolve`, or `filter` (filter adjudicates raw massdns output via `-ri/-raw-input`) |
| `-t` | concurrent massdns resolves (default 10000) — your main throttle |
| `-mcmd/-massdns-cmd` | extra args passed through to massdns (e.g. `-i` interval) |
| `-m/-massdns` | path to the massdns binary |
| `-retries` | DNS retries (default 5) — multiplies query volume |
| `-sw/-strict-wildcard` | wildcard-check every found name |
| `-wt` | concurrent wildcard checks (default 250) |
| `-wo/-wildcard-output` | dump wildcard IPs for audit |
| `-o/-output`, `-j/-json` | output file; ndjson output |
| `-silent` | names only, for clean piping |

---

## Pitfalls, noise and scope discipline

How hunters get reports rejected — or get throttled / removed from a program — with this
tool, and the settings that prevent it:

- **Running bruteforce without checking the active-recon clause.** Bruteforce mode is
  high-volume active DNS against the target's authoritative nameservers. On a program that
  restricts automated scanning it is a rules violation regardless of whether you find
  anything. *Prevent:* Phase 0 gate; default to resolve mode; ask before bruteforcing.
- **Default `-t 10000` on a courtesy-sensitive target.** 10,000 concurrent lookups plus
  `-retries 5` and a large wordlist is a traffic spike the target (and your own ISP) will
  notice. The massdns README warns the usage "may cause a significant load on the used
  resolvers and result in abuse complaints being sent to your ISP." *Prevent:* lower `-t`,
  pass `-mcmd "-i <ms>"`, trim the wordlist.
- **Unvalidated / stale resolvers → false-positive subdomains.** Dysfunctional or
  spoofing resolvers invent hosts that do not exist. Report one and you look like you ran
  a scanner you do not understand. *Prevent:* validate the list (dnsvalidator / trickest),
  and always do a trusted-resolver re-validation pass before trusting a name.
- **Bruteforcing a wildcard apex.** If `*.example.com` answers everything, bruteforce
  burns hundreds of thousands of queries to produce a list of IPs that are all the same
  wildcard target. *Prevent:* detect the wildcard first; rely on the built-in filter, add
  `-sw` on wildcard-heavy domains, and review `-wo` output before believing the hits.
- **Leaking your target's guessed names to public resolvers.** Every bruteforce query for
  `secret-admin.example.com` is logged by whichever public resolver handled it. On a
  sensitive engagement that is an information-disclosure problem of your own making.
  *Prevent:* resolve sensitive confirmations through trusted resolvers (or a local unbound
  instance, per the massdns README) rather than the big public list.
- **Treating the output as the finding.** A raw subdomain list is the lowest-value,
  highest-dupe thing you can submit; many programs auto-close it. *Prevent:* always pivot
  (takeover check, httpx, your methodology) and report the downstream impact.
- **Scope drift via CNAMEs.** A resolved in-scope name may CNAME to out-of-scope infra.
  Enumerating or attacking that target is outside authorization. *Prevent:* keep `-d` to
  in-scope apexes; when a CNAME leaves scope, check the program's third-party-takeover
  policy before acting.

---

## Reporting evidence

shuffledns-sourced findings live or die on reproducibility and on showing you did not
invent the host. Capture:

- **The exact command(s)**, including `-mode`, the resolver list source (URL + date you
  pulled it), `-t`, and any `-mcmd` throttle — so the triager can see the volume you used
  and that it was reasonable.
- **The resolved name plus its resolution path.** For a live host, the shuffledns line
  and an independent confirmation: `dig api.example.com @1.1.1.1 +short` (or the `-j`
  ndjson record showing host + IP). Independent re-resolution through a trusted resolver
  is what proves the name is real and not a resolver artifact.
- **For a takeover:** the CNAME chain (`dig <host> CNAME +short`), the dangling target,
  the third-party service's "not found / claim this" response, and — only if the program
  permits proof-of-takeover — a minimal, clearly-marked claim on a resource you control.
  Never host real content; a timestamped benign marker is enough.
- **For a surface-expansion pivot:** the httpx line (status, title, tech) for the host,
  then the actual downstream vulnerability's own evidence. The shuffledns output is the
  trail, not the finding.
- **Wildcard context when relevant:** the `-wo` wildcard-IP file, to pre-empt the "isn't
  this just a wildcard?" triage question.
- **Scope proof:** the in-scope apex, and confirmation the reported name is a subdomain of
  it — not an out-of-scope CNAME target.

Keep volume proportional: capture the one host and its resolution, not a 10,000-line dump.
The report is the live host and its impact; the scan log is supporting material.

---

## References

- shuffledns — repository and README (flags, modes, install, wildcard notes): https://github.com/projectdiscovery/shuffledns
- shuffledns README (raw, main branch) — verified flag/help source: https://raw.githubusercontent.com/projectdiscovery/shuffledns/main/README.md
- massdns (blechschmidt) — `-s` concurrency, `-o` output flags, resolver/load warnings: https://github.com/blechschmidt/massdns
- trickest/resolvers — continuously-validated public resolver lists (`resolvers.txt`, `resolvers-trusted.txt`): https://github.com/trickest/resolvers
- trickest/resolvers raw list: https://raw.githubusercontent.com/trickest/resolvers/main/resolvers.txt
- vortexau/dnsvalidator — build/validate an untainted resolver list; thread-count warning: https://github.com/vortexau/dnsvalidator
- subfinder — passive source for the resolve-mode pipeline: https://github.com/projectdiscovery/subfinder
- commonspeak2 wordlists (bruteforce input, linked from the README): https://github.com/assetnote/commonspeak2-wordlists

*Research supplement added 2026-11-16, grounded in the shuffledns README (github.com/projectdiscovery/shuffledns), the massdns README (github.com/blechschmidt/massdns), trickest/resolvers, and vortexau/dnsvalidator; the ProjectDiscovery hosted docs pages returned 404 at time of writing, so all flag/feature claims are grounded in the current repository README help output (model: claude-opus-4-8).*
