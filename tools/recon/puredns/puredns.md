---
name: "puredns"
categories:
  - blackarch-recon
version: "v2.1.1.r4.g46bd4c9"
homepage: "https://github.com/d3mondev/puredns"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-03-01"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "d3mondev/puredns"
github-stars: 2262
github-pushed: "2026-02-23"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# puredns

> Fast domain resolver and subdomain bruteforcer built on massdns, with wildcard detection and DNS-poisoning filtering that plain massdns lacks.

## What it's for

puredns wraps massdns bulk resolution with the two things massdns does not do well: filtering wildcard subdomains (`*.example.com` answering for every name) and validating answers against trusted resolvers to drop DNS-poisoned or load-balanced junk. Given a wordlist and resolver list it resolves millions of queries in minutes and hands you only genuinely resolvable domains. It needs `massdns` installed plus a list of public resolvers (a `resolvers.txt` in the working directory, `~/.config/puredns/resolvers.txt`/`resolvers-trusted.txt`, or the `--resolvers`/`--resolvers-trusted` flags; without any, it falls back to 8.8.8.8/8.8.4.4). It resolves and filters — it does not bruteforce HTTP content or enumerate via CT logs.

## Typical use cases

### Bruteforce subdomains for one domain

Mass-resolve a wordlist against a root domain with wildcard filtering.

```
puredns bruteforce wordlist.txt example.com
```

Outcome: runs the wordlist through massdns, detects wildcard roots with minimal extra queries, validates answers via trusted resolvers, and prints only valid, resolvable subdomains.

### Bruteforce across many root domains

Feed a file of root domains instead of a single argument.

```
puredns bruteforce wordlist.txt -d domains.txt
```

Outcome: same resolution and wildcard-filtering pipeline applied to every domain in `domains.txt`, one per line.

### Resolve an existing list of domains

Clean up a mixed list of candidate hostnames.

```
puredns resolve domains.txt
```

Outcome: resolves the list and prints only the domains that genuinely resolve — wildcard and poisoned entries are dropped, so the output is safe to pipe into content probes.

### Pipe quietly into other tools

Chain resolution into an automation pipeline.

```
cat domains.txt | puredns resolve -q | httprobe
```

Outcome: quiet mode prints bare valid domains, which httprobe then turns into live HTTP host candidates; the same `-q` pattern works for `puredns bruteforce` with a wordlist on stdin.

### Save full results to files

Keep domains, wildcards, and raw massdns output for later reuse.

```
puredns resolve domains.txt --write valid_domains.txt \
                            --write-wildcards wildcards.txt \
                            --write-massdns massdns.txt
```

Outcome: writes three files — the clean domain list, the wildcard root domains found, and the raw massdns `-o Snl` output (useful for extracting A/CNAME records later).

### Pin your own resolver sets

Control exactly which servers answer, and which are trusted for validation.

```
puredns resolve domains.txt --resolvers public-resolvers.txt --resolvers-trusted trusted-resolvers.txt
```

Outcome: uses the given public resolvers for bulk lookups and the trusted list for wildcard/poisoning validation — the recommended setup once you have curated resolver lists.

No corpus evidence — derived from upstream documentation.

---

## Bug bounty workflow with puredns

puredns is a DNS tool, not an HTTP tool: it never touches the target's web servers, only resolves names against public and trusted DNS resolvers. That makes it one of the quietest-to-the-target recon steps you run — but "quiet to the target" is not "quiet to the resolvers," and an unbounded bruteforce against a small authoritative nameserver or a free public resolver pool is exactly how hunters get their resolver lists blacklisted and their result sets poisoned. Every phase below assumes the root domain (`example.com` / `*.example.com`) is in the program's written scope, and that wildcard subdomains are permitted targets, before you send a query.

The output of puredns is never itself the finding. A resolvable subdomain is attack surface; the bug lives in what is *served* there (a takeover, a forgotten admin panel, a staging app with debug on). puredns's job is to hand the next tool a list that is **real** — no wildcard phantoms, no DNS-poisoned junk, no load-balancer artefacts — so you do not waste a day (or a triager's patience) chasing hosts that never existed.

### Phase 0 — Scope, authorization, and resolver hygiene

Two preconditions decide whether puredns produces evidence or noise:

- **Scope.** Confirm the program lists the apex and that `*.example.com` (or the specific subdomains) are in scope. A wildcard DNS record (`*.example.com A 198.51.100.10`) means *every* label resolves — bruteforcing it without wildcard filtering yields a list of hundreds of thousands of "live" hosts that are all the same catch-all. Reporting those as discovered assets is the fastest way to a *Not Applicable*.
- **Resolver list.** puredns is only as trustworthy as its resolvers. Stale, hijacked, or ISP-injecting public resolvers return answers for names that do not exist, which is precisely the false-positive puredns is supposed to kill — a bad `--resolvers` file defeats the tool. Curate one before you start:

```
# Trickest keeps a frequently-refreshed, validated public-resolver list
wget -q https://raw.githubusercontent.com/trickest/resolvers/main/resolvers.txt \
     -O resolvers.txt

# Or build your own from public-dns.info and validate with dnsvalidator
dnsvalidator -tL https://public-dns.info/nameservers.txt -threads 20 -o resolvers.txt
```

Keep a **separate, tiny, trusted** list for the validation pass — resolvers you control or trust absolutely (your own unbound, or the Google/Cloudflare anycast IPs). With no `--resolvers-trusted` file and none at `~/.config/puredns/resolvers-trusted.txt`, puredns validates against `8.8.8.8` and `8.8.4.4` by default. Trusted resolvers must be few and clean: they are the court of final appeal that decides which names survive.

### Phase 1 — Passive-first: resolve before you bruteforce

The lowest-noise, highest-yield first move is **not** a bruteforce. Gather candidate subdomains passively (CT logs, `subfinder`, `amass -passive`, GAU/wayback), then use `puredns resolve` to strip that list down to what is actually live — cheap, fast, and it never sends a single speculative label at the target's nameservers.

```
subfinder -d example.com -silent | puredns resolve -r resolvers.txt -q > live.txt
```

This alone often surfaces the interesting hosts (a `jira-staging.example.com` that a dev leaked into a CT-logged cert) without any bruteforce at all, and it establishes the wildcard situation before you commit to the loud phase.

### Phase 2 — Wildcard reconnaissance

Before bruteforcing, learn whether the apex is a wildcard and how aggressively it load-balances. Resolve a handful of guaranteed-nonexistent random labels:

```
printf '%s\n' nonexistent-$RANDOM.example.com zzz-$RANDOM.example.com | \
  puredns resolve -r resolvers.txt --write-wildcards wildcards.txt -q
```

If random garbage resolves, the domain is a wildcard. Heavily load-balanced wildcards rotate answers across many IPs, which can defeat the default of **3** wildcard tests (`--wildcard-tests` / `-n 3`). The README notes some domains need more than 50 tests before detection stabilises — raise `-n` for those (Phase 3's recipe shows it). Capturing the wildcard roots now (`--write-wildcards`) also feeds Phase 4: a wildcard CNAME pointing at a deprovisioned SaaS is a takeover lead, not just noise to discard.

### Phase 3 — Controlled bruteforce

Only now run the wordlist, and run it *rate-limited*. The public-resolver rate limit (`--rate-limit` / `-l`) defaults to **0 = unlimited**; leaving it unlimited is the single biggest scope-discipline mistake with this tool.

```
puredns bruteforce wordlist.txt example.com \
  -r resolvers.txt --resolvers-trusted trusted.txt \
  --rate-limit 1500 --rate-limit-trusted 400 \
  -n 15 \
  -w valid.txt --write-wildcards wildcards.txt --write-massdns massdns.txt -q
```

puredns runs three internal stages: (1) bulk-resolve the wordlist through massdns against the public resolvers; (2) run the wildcard-detection algorithm, using the stage-1 massdns output as a DNS cache to minimise extra queries and circumvent load-balancing; (3) re-validate the survivors against the trusted resolvers at a slower pace to drop DNS-poisoned and transient answers. The three `--write-*` files are your evidence trail (Phase 5).

### Phase 4 — Takeover triage

puredns deliberately does **not** throw away `NXDOMAIN` answers that carry a `CNAME` — an alias pointing at an unclaimed/expired third-party host is the classic subdomain-takeover primitive. Pull A/CNAME records out of the raw massdns file and feed the wildcard roots and dangling CNAMEs into takeover tooling:

```
# CNAME targets from the raw massdns (-o Snl) database
awk '$2=="CNAME"{print $1" -> "$3}' massdns.txt | sort -u > cnames.txt
# then: nuclei -t dns/, subzy, or dnsReaper against candidates
```

A resolving subdomain whose CNAME target returns a provider "no such bucket / app not found" page is a takeover finding — High, and worth confirming manually before reporting.

### Phase 5 — Handoff to the HTTP surface

The clean list goes to the web tools, which is where most payouts actually materialise:

```
puredns resolve candidates.txt -r resolvers.txt -q | httpx -silent | nuclei -silent
```

Because the list is wildcard-filtered and trusted-validated, httpx is not wasting probes on phantom hosts, and nuclei's findings are anchored to assets that demonstrably exist.

## Where the money is with puredns

puredns finds *assets*; the bounty is on what those assets expose. The table maps each finding class to the puredns feature that surfaces it.

| Finding class | puredns feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| Subdomain takeover (dangling CNAME) | NXDOMAIN-with-CNAME retention + `--write-massdns` → extract CNAMEs | High | Medium — popular apex names get re-hunted; move fast |
| Forgotten/unlinked host (staging, admin, debug) | `bruteforce` with a deep wordlist, wildcard-filtered | depends on what's served (Low→Critical) | Medium–High on big programs |
| Wildcard-hidden real subdomain | aggressive `--wildcard-tests` separating real names from the catch-all | depends on service | Lower — wildcard noise deters lazy hunters |
| Internal/stale name leaking infra | `resolve` of a passive CT/wordlist corpus, trusted-validated | Info→Medium | Medium |
| New asset in a monitored program | periodic `resolve`/`bruteforce` diffed against a prior run | first-finder premium | Low if you diff continuously |
| Origin-IP / pre-CDN host exposure | resolving non-proxied siblings (`origin.`, `direct.`, `mail.`) | Medium (WAF bypass) | Medium |

The recurring truth: **the subdomain is never the report.** "I found 4,000 subdomains" is not a submission. "`assets-staging.example.com` is a dangling CNAME to an unclaimed S3 bucket, PoC below" is. puredns earns its place by making the 4,000 trustworthy enough that the one that matters is not buried in wildcard phantoms.

## How puredns decides a name is real (the three stages)

Understanding the pipeline is what lets you trust the output — and explain it to a triager who asks "how do you know this host exists?" puredns runs massdns three times, each with a different purpose.

**Stage 1 — bulk resolution.** The sanitised input (a wordlist expanded against the apex for `bruteforce`, or the list as-is for `resolve`) is handed to massdns, which fires queries against the **public** resolver pool (`--resolvers`, default `./resolvers.txt` → `~/.config/puredns/resolvers.txt`) at up to `--rate-limit` queries/second. This is the fast, loud, low-trust stage: massdns records every answer, including wildcard catch-alls and anything a hijacking resolver injects. Its raw output becomes a local DNS cache for the next stage, which is why puredns is economical with queries — it does not re-ask what stage 1 already learned.

**Stage 2 — wildcard detection and filtering.** puredns extracts the wildcard subdomain *roots* from the stage-1 results using its own algorithm. The key design points, from the README: it uses the stage-1 massdns output as a cache to minimise fresh queries, it performs `--wildcard-tests` (`-n`, default **3**) probes with random labels to characterise each candidate root, and it is built to **circumvent DNS load-balancing** — a wildcard that answers from a rotating pool of IPs will not look identical across probes, so too few tests under-detect it. When the algorithm needs to confirm a cache entry it issues a real query; otherwise it reasons from the cache. The roots it identifies are written out by `--write-wildcards`, and the names that only resolved *because* of a wildcard are dropped from the valid set. On a 1 GB-RAM VPS, `--wildcard-batch` (default `0` = one batch) bounds how many names are held in memory at once during this stage.

**Stage 3 — trusted validation.** The survivors are resolved one final time against the **trusted** resolvers (`--resolvers-trusted`, default `8.8.8.8`/`8.8.4.4`) at the slower `--rate-limit-trusted` pace (default `500` q/s) to avoid tripping their throttles. A name that resolved via a flaky or poisoned public resolver but fails here is discarded; only names that a trusted resolver confirms survive into `--write` (`-w`). `--skip-validation` removes this stage, and `--trusted-only` *replaces* stages 1 and 3 with a single trusted-resolver pass (implying skip-validation). This stage is why puredns output is safe to report: a surviving name was confirmed by a resolver you trust, not merely by whatever public server answered first.

### Output normalization

The input side is normalised before stage 1: puredns sanitises the candidate list so only entries containing valid hostname characters (essentially `[a-z0-9.-]`) are tested, lowercasing and dropping malformed labels, and recent builds enforce the 253-character DNS name limit and handle underscores. `--skip-sanitize` turns this off — only do that when your wordlist deliberately carries characters the sanitiser would strip (certain IDNA or `_dmarc`-style names). On the output side, `-q`/`--quiet` reduces stdout to bare validated hostnames with no banner or progress, which is the form every downstream tool (`httpx`, `nuclei`, `dnsgen`) expects on a pipe. The `--write-massdns` file preserves the full records in massdns `-o Snl` simple-text form (`name TYPE data` per line) when you need the A/CNAME data, not just the names.

### Wordlist discipline (bruteforce mode)

`bruteforce` multiplies: every wordlist entry becomes one query per apex, so a 5-million-line list against 10 roots is 50M queries. Discipline here is both a courtesy and a signal-quality issue:

- **Match wordlist size to rate limit and scope.** A deep list (`jhaddix` `all.txt`, `n0kovo_subdomains`) is justified on a single high-value apex with `--rate-limit` set; a quick `dns-small`/`dns-common` list is right for a first sweep across many roots.
- **Permutations belong in a wordlist tool, not here.** Generate candidate names with a permutation engine, then let puredns resolve them — do not try to make puredns guess patterns. This is the highest-yield combo for finding the host nobody else did:

```
# permute known subdomains, then resolve the candidates through puredns
alterx -l known_subs.txt | puredns resolve -r resolvers.txt --rate-limit 1500 -q
# dnsgen works the same way: dnsgen known_subs.txt | puredns resolve ...
```

Cross-reference the permutation step with [[alterx]]. Feeding puredns a *targeted* permutation list beats blasting a generic 10M wordlist: fewer queries, less noise, and the names are shaped like the target's real naming convention.

## Command recipes

Each block is a real invocation with the low-volume setting called out. Flags and defaults below are from the current source (`internal/app/cmd`, commit `46bd4c9`, matching the packaged `v2.1.1.r4`).

**Clean a passive list (cheapest, quietest).**
```
cat passive_subs.txt | puredns resolve -r resolvers.txt -q
# -> bare valid, wildcard-filtered, trusted-validated hostnames on stdout
```
`resolve` reads stdin or a file argument. `-q`/`--quiet` prints only the resolved domains (no banner/progress), so the output pipes straight into `httpx`/`httprobe`.

**Bruteforce one domain with full evidence capture.**
```
puredns bruteforce wordlist.txt example.com \
  -r resolvers.txt --resolvers-trusted trusted.txt \
  --rate-limit 1500 \
  -w valid.txt --write-wildcards wildcards.txt --write-massdns massdns.txt
# valid.txt       -> clean resolvable subdomains
# wildcards.txt   -> wildcard subdomain roots that were filtered out
# massdns.txt     -> raw massdns results in -o Snl (simple text) format: "name TYPE data"
```
`--rate-limit 1500` caps **public**-resolver queries at 1500/s total (default is `0`, unlimited — always set this). `--rate-limit-trusted` defaults to `500`; lower it (e.g. `400`) if your trusted resolvers are a small self-hosted set.

**Bruteforce across many apexes.**
```
puredns bruteforce wordlist.txt -d scope_roots.txt -r resolvers.txt --rate-limit 1500 -q
```
`-d`/`--domains` takes a file of in-scope root domains (bruteforce only). Keep the file to apexes the program actually lists.

**Tame a heavily load-balanced wildcard.**
```
puredns bruteforce wordlist.txt example.com -r resolvers.txt -n 50 --rate-limit 1000
```
`-n`/`--wildcard-tests` defaults to **3**, which the README itself calls "very low." Raise it (25–50+) when a domain rotates wildcard answers across many IPs; each extra test costs queries, so pair it with a sane `--rate-limit`.

**Cap memory on a large run (low-RAM VPS).**
```
puredns bruteforce huge_wordlist.txt example.com -r resolvers.txt --wildcard-batch 1000000
```
`--wildcard-batch` defaults to `0` (all domains in one batch, fastest, most RAM). The author suggests 1M–2M subdomains per batch on a 1 GB-RAM VPS. Splitting trades a few more queries for bounded memory.

**Trusted-resolvers-only (no public pool).**
```
puredns resolve candidates.txt --resolvers-trusted trusted.txt --trusted-only -q
```
`--trusted-only` resolves entirely through the trusted list and **implies `--skip-validation`** (there is nothing left to re-validate). Useful when you only trust a handful of resolvers and the candidate list is small; too slow for a full bruteforce.

**Continuous monitoring — diff runs to catch new assets first.**
```
# nightly: resolve the full candidate corpus, snapshot, diff against yesterday
puredns resolve corpus.txt -r resolvers.txt -q | sort > today.txt
comm -13 yesterday.txt today.txt > new_hosts.txt   # names live today, absent yesterday
```
New subdomains are the freshest, least-reviewed assets on a program — a host that appeared overnight is where the new (and often misconfigured) code lives. Resolving a stable corpus on a schedule and diffing is a first-finder engine; keep the rate limited and the corpus in scope. Pair with the Phase 4 takeover triage, since newly-pointed CNAMEs are prime takeover candidates before the owner notices.

**Point at a non-PATH massdns and keep intermediates for debugging.**
```
puredns resolve domains.txt -b /opt/massdns/bin/massdns --debug -r resolvers.txt
```
`-b`/`--bin` defaults to `massdns` (resolved from `$PATH`). `--debug` is a global flag that keeps the intermediate working files instead of cleaning them up — useful when a run returns surprising counts and you want to inspect the raw stages.

**Skip flags — know what each one turns off (and why you usually should not):**

| Flag | Disables | When it is defensible |
|---|---|---|
| `--skip-wildcard-filter` | the entire wildcard-detection stage | never on a wildcard domain; only when you have *already* filtered and want raw resolution |
| `--skip-validation` | the trusted-resolver re-check | only when your public resolvers are already fully trusted |
| `--skip-sanitize` | input sanitisation (keeps non-`[a-z0-9.-]` entries) | when your wordlist intentionally contains underscores/IDNA you must preserve |

Note the exact flag name is `--skip-wildcard-filter` (older docs/changelog said `--skip-wildcard`; the current binary uses the longer name).

## Pitfalls, noise and scope discipline

**Unbounded rate is the cardinal sin.** `--rate-limit 0` (the default) lets puredns fire as fast as massdns can push packets. Against a small target's authoritative nameservers that looks like a flood; against free public resolvers it gets your source IP rate-limited or null-routed, after which your results silently degrade. Set `--rate-limit` on every active run, and keep `--rate-limit-trusted` modest so you do not trip Google/Cloudflare's per-source throttles mid-validation.

**A dirty resolver list manufactures false positives.** The whole value proposition — "only genuinely resolvable domains" — collapses if `--resolvers` contains hijacking or NXDOMAIN-rewriting servers. Those return answers for names that do not exist; puredns's trusted-validation pass catches most, but a poisoned *trusted* list is unrecoverable. Refresh the Trickest list (or re-run dnsvalidator) regularly, and avoid resolvers in jurisdictions that inject DNS responses.

**Wildcard domains without filtering = junk reports.** Submitting a wall of `*.example.com` catch-all hosts as "discovered subdomains" is a textbook *Informational/N-A* and burns reputation. Never pass `--skip-wildcard-filter` on a domain you have not already proven is non-wildcard. When a domain is heavily load-balanced, a too-low `--wildcard-tests` under-detects and leaks phantom hosts into your output — raise `-n` rather than trusting the default 3.

**puredns keeps NXDOMAIN-with-CNAME on purpose — do not mistake it for noise.** Those entries are the takeover leads, not errors. Equally, do not report every dangling CNAME as a takeover without confirming the target is actually claimable; a CNAME to a live, owned host is normal.

**massdns must be installed and compatible.** puredns is a wrapper; if `massdns` is absent from `$PATH` and you did not pass `-b`, it fails outright. Build massdns from `github.com/blechschmidt/massdns` (`make && sudo make install`) or point `-b` at the binary.

**Stay inside scope.** Only feed `-d`/apex arguments that the program lists. Resolving an out-of-scope sibling domain "for context" is still an active DNS probe against assets you were not authorized to touch, and a wordlist of internal-sounding labels aimed at the wrong apex is indistinguishable from reconnaissance for an attack.

**Where puredns fits vs. its neighbours (choice tree):**

| Tool | What it is | Reach for it when |
|---|---|---|
| [[massdns]] | the raw stub-resolver engine underneath both wrappers; no wildcard handling, no validation | you want maximum speed and will do your own wildcard/poison filtering downstream |
| [[shuffledns]] | ProjectDiscovery's massdns wrapper; wildcard filtering + resolve/bruteforce, a *different* wildcard algorithm, native fit with `subfinder`→`httpx`→`nuclei` | you already live in the PD toolchain and want seamless stdin/stdout chaining |
| puredns | massdns wrapper with aggressive wildcard-detection (load-balancing circumvention) + a dedicated trusted-resolver validation pass + takeover-friendly NXDOMAIN/CNAME retention | you want the strongest false-positive filtering and clean takeover leads, and are willing to curate resolver lists |

All three sit on massdns; the difference is how hard they work to make the output *trustworthy*. puredns optimises for precision (fewest phantoms, poisoning dropped), which is exactly what keeps bounty reports from bouncing.

## Reporting evidence

puredns findings are DNS facts; capture them so a triager can reproduce them without your resolver list.

- **The exact command**, including the resolver lists used and the `--rate-limit`/`-n` values. Reproducibility of a DNS result depends on *which* resolvers answered; note them.
- **The raw massdns record**, not just the hostname. From `--write-massdns` (`-o Snl` simple text), the line `assets-staging.example.com. CNAME d1234.cloudfront.net.` is the primary evidence for a takeover; include the full record, type, and data.
- **An independent confirmation** that a triager can run: `dig +short assets-staging.example.com CNAME @8.8.8.8` and the response. A finding that only reproduces through your resolver list is weak — show it resolves on a public, named resolver.
- **For takeovers:** the resolving CNAME chain *plus* the provider's "unclaimed" response (the HTTP body / error page proving the target is claimable), captured as a request/response pair. The DNS record alone is necessary but not sufficient.
- **For wildcard-separated real hosts:** show the wildcard baseline (a random label and its catch-all answer) next to the real host (a different, stable answer), so the triager sees why the host is genuine and not a wildcard artefact. The `--write-wildcards` file documents exactly which roots were filtered.
- **Never over-collect.** DNS enumeration is cheap to run huge; resist dumping tens of thousands of names into a report. Submit the specific asset and its proof; keep the full list as your own working artefact.

### Worked example — subdomain takeover evidence

A complete, triager-ready evidence block for a takeover surfaced through puredns looks like this:

```
# 1. puredns command that surfaced the dangling name
puredns bruteforce dns-wordlist.txt example.com \
  -r resolvers.txt --rate-limit 1500 --write-massdns massdns.txt

# 2. the record from the raw massdns database (primary DNS evidence)
grep -i 'assets-staging' massdns.txt
# assets-staging.example.com. CNAME d111abc.cloudfront.net.

# 3. independent confirmation on a named public resolver (triager can repeat this)
dig +short assets-staging.example.com CNAME @8.8.8.8
# d111abc.cloudfront.net.

# 4. proof the CNAME target is unclaimed (the HTTP response that makes it exploitable)
curl -sI https://assets-staging.example.com/
# HTTP/1.1 404 Not Found
# ... "The request could not be satisfied ... Bad Request" (CloudFront: distribution not found)
```

The four parts together are what a takeover report needs: the discovery command, the raw DNS record, a reproduction on a resolver the triager trusts, and the HTTP response proving the target is claimable. The DNS record alone is not a finding — step 4 is what distinguishes a takeover from a harmless alias. Do not actually claim the resource; demonstrating that it *is* claimable (the provider's "not found" response) is the proof, and claiming it would be acting outside authorization.

## Flag quick reference

Complete flag set with defaults, read from source (`internal/app/cmd/*.go`, `internal/app/ctx/options.go`) at the packaged commit `46bd4c9`. `resolve` and `bruteforce` share every flag except `--domains`; `--quiet` and `--debug` are global (root) flags.

| Flag | Short | Default | Purpose |
|---|---|---|---|
| `--resolvers` | `-r` | `./resolvers.txt` → `~/.config/puredns/resolvers.txt` | public resolver list (bulk stage) |
| `--resolvers-trusted` | | empty → `8.8.8.8`,`8.8.4.4` | trusted resolver list (validation stage) |
| `--rate-limit` | `-l` | `0` (unlimited) | queries/sec cap for public resolvers — **always set this** |
| `--rate-limit-trusted` | | `500` | queries/sec cap for trusted resolvers |
| `--wildcard-tests` | `-n` | `3` | probes per root to beat DNS load-balancing — raise for balanced wildcards |
| `--wildcard-batch` | | `0` (one batch) | subdomains per wildcard batch — set ~1–2M on 1 GB RAM |
| `--threads` | `-t` | `100` | threads used during wildcard filtering |
| `--bin` | `-b` | `massdns` (from `$PATH`) | path to the massdns binary |
| `--domains` | `-d` | empty | file of apex domains (bruteforce only) |
| `--write` | `-w` | empty | write validated domains to file |
| `--write-wildcards` | | empty | write detected wildcard roots to file |
| `--write-massdns` | | empty | write raw massdns results (`-o Snl`) to file |
| `--trusted-only` | | `false` | resolve only via trusted resolvers (implies `--skip-validation`) |
| `--skip-wildcard-filter` | | `false` | disable wildcard detection/filtering |
| `--skip-validation` | | `false` | disable the trusted-resolver re-check |
| `--skip-sanitize` | | `false` | keep entries with non-`[a-z0-9.-]` characters |
| `--quiet` | `-q` | `false` | bare validated hostnames on stdout (global) |
| `--debug` | | `false` | keep intermediate files (global) |

## References

- puredns repository and README — https://github.com/d3mondev/puredns
- puredns flag definitions (source, `internal/app/cmd/resolve.go`, `bruteforce.go`, `root.go`) — https://github.com/d3mondev/puredns/tree/master/internal/app/cmd
- puredns option defaults (source, `internal/app/ctx/options.go`) — https://github.com/d3mondev/puredns/blob/master/internal/app/ctx/options.go
- puredns CHANGELOG — https://github.com/d3mondev/puredns/blob/master/CHANGELOG.md
- massdns (the underlying resolver) — https://github.com/blechschmidt/massdns
- Trickest curated public-resolver list — https://github.com/trickest/resolvers
- dnsvalidator (build/validate resolver lists) — https://github.com/vortexau/dnsvalidator
- shuffledns (alternative massdns wrapper) — https://github.com/projectdiscovery/shuffledns

*Research supplement added 2026-11-16, grounded in the puredns repository README, CHANGELOG, and flag/default definitions read from source at commit 46bd4c9 (github.com/d3mondev/puredns), cross-referenced with massdns, Trickest resolvers, and dnsvalidator upstreams (model: claude-opus-4-8).*
