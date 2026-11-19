---
name: "findomain"
categories:
  - recon
homepage: "https://github.com/Findomain/Findomain"
origin: "pentest-book"
recipe: "upstream"
packaged-by: [arch, nixpkgs]
build-system: rust
github-repo: "Findomain/Findomain"
github-stars: 3816
github-match: "exact"
gh-checked: "2026-11-13"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
last-refresh: "2026-11-19"
license: "GPL-3.0"
---

# findomain

> Subdomain enumeration via Certificate Transparency logs and 50+ passive APIs, with resolution, port checks, screenshots, and webhook monitoring.

## What it's for

Findomain discovers subdomains without brute force: it queries Certificate Transparency logs and roughly 54 passive sources in parallel — many of which require an access token — then resolves what it found so you see which names actually live. Beyond one-shot enumeration it can monitor targets over time (storing findings and alerting to Discord, Slack, or Telegram webhooks), brute-force with a wordlist, import other tools' output for unified resolution and reporting, check HTTP status, take screenshots of live hosts, and scan ports. Reach for it at the start of external recon to build the target's host inventory in seconds; note that the sources needing tokens (`**` in the README's source list) stay silent until you configure them, and the archive indexes (Wayback, CommonCrawl) are the slow ones worth excluding on quick passes.

## Typical use cases

### Enumerate a domain passively

Build the first host inventory for an external target.

```
findomain -t example.com
```

Outcome: all free passive sources are queried in parallel and discovered subdomains print to stdout; sources are walked to their last page, so a run settles in roughly twenty seconds on a normal domain.

### Keep only resolvable subdomains

Filter out dead names before feeding them to other tools.

```
findomain -t example.com -r
```

Outcome: only subdomains that resolve are shown and written; use `-i` instead to also print each subdomain's IP address (the two flags are mutually exclusive).

### Write results to one named file

Keep a per-engagement artifact instead of stdout.

```
findomain -t example.com -u subdomains.txt
```

Outcome: all results for the target are written to the named file (`-o` instead writes an automatically named `<target>.txt`).

### Import other tools' output and resolve it together

Dedupe and validate what subfinder/amass already produced.

```
findomain -t example.com --import-subdomains subfinder.txt amass.txt -r
```

Outcome: the imported names join Findomain's own discoveries and the combined set goes through the same resolution, filtering, and reporting in one run.

### Skip the slow archive sources

Cut minutes off a quick pass.

```
findomain -t example.com --exclude-sources wayback,commoncrawl
```

Outcome: the named sources are skipped entirely; `--source-timeout`, `--source-budget`, and `--archive-budget` bound the remaining worst cases instead of excluding them.

### Brute-force with a wordlist

Augment passive finds with guessed names.

```
findomain -t example.com -w subdomains.txt
```

Outcome: brute-force mode is enabled automatically; each name from the wordlist is tried under the target domain and survivors are resolved and reported like any other find.

No corpus evidence — derived from upstream documentation.

---

## Bug bounty workflow with findomain

Findomain is a passive-first host-inventory builder, and the single most important thing to internalize before you run it is that **the enumeration output is almost never the finding**. A list of subdomains is a map, not a vulnerability. Every other hunter on the program runs findomain, subfinder, and amass against the same apex domains, so the raw name list is a commodity — reporting "I found `staging.example.com`" earns a `Duplicate` or `Informative` close in minutes. Your edge comes from three places findomain actually gives you: (1) configuring the keyed sources that most hunters skip, so your inventory is *wider* than the default-config crowd's; (2) monitoring mode catching newly-created names *first*, before the subdomain is claimed or hardened; and (3) the triage you do on the inventory — takeover candidates, forgotten admin panels, pre-prod hosts — which is where the actual payouts live.

Everything below assumes an authorized program: you have written permission, you respect the scope list (apex domains, in-scope wildcards, whether acquisitions count), and you keep volume low. Passive enumeration is low-risk by nature — you are querying third-party APIs, not the target — but findomain also bundles *active* modules (brute force, DNS resolution floods, `nmap` port scanning, `nuclei`, `ffuf`) that touch the target directly and can breach scope or cause disruption. Those are gated behind their own flags and treated as a separate, authorization-required phase at the end.

### Phase 0 — Scope intake and key configuration

Goal: know exactly which names are in scope, and make findomain's "55 sources" headline actually true for your run.

The free-tier reality check first. Findomain advertises 55 passive sources, but the README marks roughly 40 of them with `**` (an API token is **required**) and two with `*` (token optional). With zero configuration you get only the no-token sources — crt.sh's database mirror, CommonCrawl, the Wayback Machine, Urlscan.io, AnubisDB, Threatminer, Sublist3r, Subdomain Center, Maltiverse, Mnemonic PassiveDNS, crt.name, the UK Web Archive, and Arquivo.pt — plus Facebook CT (findomain ships a built-in token since 0.2.4) and CertSpotter/HackerTarget (optional tokens). That default set is perhaps a quarter of the advertised reach, and it is exactly the set every other default-config hunter also has. The high-value sources (SecurityTrails, VirusTotal, Censys, Shodan, BinaryEdge, FullHunt, Chaos, LeakIX, Netlas, PassiveTotal, ZoomEye…) all need keys, and several of them have free registration tiers. Configuring even the free-to-register ones is the cheapest coverage advantage in the whole workflow.

```bash
# Audit which keyed sources are actually configured — values are never printed
findomain --list-keys
```

Outcome: a table of every source that accepts a key, the config key / environment variable name, and whether one is currently set. Treat any high-signal source showing "not set" as unclaimed coverage. Set keys through their environment variables; the three with documented setup steps are `findomain_fb_token` (Facebook CT, pre-filled), `findomain_virustotal_token`, and `findomain_securitytrails_token`, and `--list-keys` prints the variable name for every other source so you never have to guess.

Record the scope decisions you will enforce with flags later: the in-scope string(s) for `--filter`, the out-of-scope brands/acquisitions for `--exclude`, and whether active probing of resolved hosts is permitted at all.

### Phase 1 — Passive sweep and output normalization

Goal: one deduplicated, in-scope, lowercase name list per engagement — the artifact every later phase and every other tool consumes.

```bash
findomain -t example.com -u example-subs.txt \
  --exclude-sources wayback,commoncrawl \
  --source-budget 180 --source-timeout 20
```

Findomain normalizes for you: results print one bare subdomain per line to stdout, lowercased and deduplicated across all sources, and `-u <file>` captures that same stream to a single named file (versus `-o`, which only toggles writing to an auto-named `<target>.txt`). For a program with several apexes, drive them from a file instead of one invocation per apex:

```bash
findomain -f scope-apexes.txt -u all-subs.txt --rate-limit 5
```

Keep the list inside scope at the source. `--filter` keeps only names containing a string; `--exclude` drops names containing one — essential when a program lists `example.com` but explicitly excludes an acquired brand that shares passive-source records:

```bash
findomain -t example.com --filter example.com --exclude internal-acq.example.com -u in-scope.txt
```

The archive indexes (Wayback, CommonCrawl, UK Web Archive, Arquivo.pt) are the slow sources; `--exclude-sources wayback,commoncrawl` drops them entirely, or bound them in place with `--archive-budget` (default 20s) while `--source-budget` (default 300s, `0` = unlimited) and `--source-timeout` (default 30s) cap everything else. On a quick pass, tightening these turns a multi-minute run into a ~20-second one.

### Phase 2 — Resolve, and keep only what is live

Goal: separate names that resolve (and respond) from dead passive-record ghosts, without a brute-force flood.

```bash
findomain -t example.com -r -u resolved.txt            # resolvable names only
findomain -t example.com -i -u resolved-with-ips.txt   # names + their IPs
```

`-r` (`--resolved`) and `-i` (`--ip`) are mutually exclusive: the first filters to resolvable names, the second also prints each name's address. Resolution concurrency is `--lightweight-threads` (default 50; the old `--threads` is a deprecated alias), and findomain resolves against Google, Cloudflare, and Quad9 unless you point `--resolvers` at your own list. For accuracy over speed, `--double-dns-check` re-checks hits against trusted resolvers to cut wildcard/poisoned false positives (only effective with custom `--resolvers`), and `--no-wildcards` disables wildcard detection when you know the zone has none. Layer an HTTP liveness check on top:

```bash
findomain -t example.com -r --http-status --http-timeout 5 -u live.txt
```

Outcome: resolved names annotated with their HTTP status, so you can go straight to the hosts that actually answer. `--http-retries` (default 2) and `--max-http-redirects` (default 0) tune that check.

### Phase 3 — Merge other tools and dedupe (where it duplicates subfinder/amass)

Goal: one unified, resolved, normalized set across every tool — and an honest answer to "does findomain add anything here?"

Findomain, subfinder, and amass share many of the same passive sources (Certificate Transparency, VirusTotal, crt.sh, etc.), so on a well-covered target their outputs overlap heavily — often 70–90%. Rather than treating that as three separate result sets, use findomain as the resolver/normalizer that merges them:

```bash
# Import others' output, add findomain's own discoveries, resolve the union once
findomain -t example.com --import-subdomains subfinder.txt amass.txt -r -u merged.txt

# Normalize/resolve an external list WITHOUT findomain enumerating at all
findomain -t example.com --import-subdomains subfinder.txt amass.txt -n -r -u merged.txt
```

`-n` (`--no-discover`) stops findomain from searching its own sources, so it becomes a pure resolve-and-dedupe pass over imported names — the right mode when subfinder/amass already ran and you only want one clean, live, deduplicated file. Where findomain earns its place in the union is the keyed sources you configured in Phase 0 that the other tools were not given keys for, and its `--cname` / `--http-status` post-processing. If `--import-subdomains` shows findomain's own discoveries are a strict subset of what subfinder already returned, say so in your notes and lean on findomain for resolution and monitoring rather than discovery on that target.

### Phase 4 — Subdomain takeover triage (the highest passive payout)

Goal: find dangling DNS records pointing at unclaimed third-party services — a real, often High-severity bug that enumeration directly enables.

This is where a name list becomes money. A subdomain whose CNAME points at a de-provisioned S3 bucket, GitHub Pages site, Heroku app, or SaaS tenant can be *claimed by you*, giving content control under the target's domain. Findomain's `--cname` tracks the CNAME each resolved name points at, which is exactly the signal takeover hunting needs:

```bash
findomain -t example.com -r --cname --http-status -u cname-map.txt
```

Outcome: resolved names with their CNAME targets and HTTP status. Sort for records pointing at third-party providers, then look for the classic tells — an HTTP body like `NoSuchBucket`, `There isn't a GitHub Pages site here`, `no such app`, or a provider 404 on a CNAME that still resolves. Confirm a candidate with a single manual request before claiming anything, and **never actually take over the subdomain** — claim it only if the program explicitly allows proof-of-takeover, otherwise prove the dangling record and stop. (If the program forbids claiming, a screenshot of the provider's "unclaimed" page plus the `dig CNAME` output is sufficient PoC.)

### Phase 5 — Monitoring mode for new scope (your durable edge)

Goal: be the first hunter to see a newly-created subdomain, because new hosts are the least-reviewed and most takeover-prone.

Monitoring mode (`-m` / `--monitoring-flag`) stores discovered subdomains in a database and alerts a webhook **only when a name is new** relative to what it has already seen. That diff is the entire value: a program's attack surface changes weekly as teams ship staging hosts, new products, and marketing microsites, and the window between "DNS record created" and "someone secures it" is where takeovers and exposed pre-prod live.

```bash
# One-time: set the webhook(s) you want alerts on
export findomain_slack_webhook="https://hooks.slack.example/..."    # or:
export findomain_discord_webhook="https://discord.example/api/webhooks/..."
# Telegram uses two vars:
export findomain_telegrambot_token="..."
export findomain_telegram_chat_id="..."

# Monitor one program, grouping its targets under a job name
findomain -m -f scope-apexes.txt -j example-program --http-status
```

Outcome: findomain enumerates, writes to its database, and pushes only the newly-found names to your webhook. `-j` / `--jobname` groups all of a program's apexes under one label so the per-program history is isolated; `--webhook-max-wait` (default 0 = wait as long as needed) controls back-off when a webhook rate-limits you, and `--aempty` sends an alert even on runs that find nothing (useful as a "the monitor is alive" heartbeat). Findomain explicitly is *not* a scheduler — run it on a cron job or systemd timer:

```cron
# Re-check every program apex list hourly; new names hit Slack
17 * * * *  findomain -m -f /opt/scope/example-apexes.txt -j example-program --http-status --quiet
```

Database reuse is what makes this scale across programs and machines. By default monitoring writes to a local SQLite file (`findomain.db`, overridable with `--sqlite <FILE>`); supplying any `--postgres-*` option (`--postgres-host`, `--postgres-user`, `--postgres-password`, `--postgres-port`, `--postgres-database`) switches storage to PostgreSQL, and a single shared Postgres server lets several monitoring hosts contribute to and dedupe against one central history. Query that history without re-enumerating:

```bash
findomain --query-database -t example.com        # everything ever seen for this target
findomain --query-jobname -j example-program      # everything under this program's job
```

`--reset-database` wipes it (destructive — it deletes all stored data), and `--no-monitor` saves to the database without entering alerting mode, so you can seed history quietly before you start caring about diffs.

### Phase 6 — Active modules (authorization-required, off by default)

Goal: go from inventory to validated finding — but only with explicit permission, because every flag in this phase sends traffic to the target.

Findomain bundles active tooling that most passive-recon users never touch. Each is opt-in and each carries scope/DoS risk:

- **Brute force** — `-w <wordlist>` enables brute-force mode and tries every word under the apex; **permutations** (`--permutations`, optional `--permutations-wordlist`) derive and resolve extra candidate names from what was already found. Both generate real DNS query volume; keep wordlists sane and consider `--rate-limit`.
- **Screenshots** — `-s <dir>` captures live hosts via Chrome/Chromium (`--screenshots-threads` default 10; `--sandbox` enables Chrome's sandbox). Fetches each page — active.
- **Port scanning** — `--pscan` runs a port sweep (`--iport` default 1, `--lport` default 1000), and `--nmap-arg`, `--nmap-full`, `--nmap-syn` (needs root), `--min-rate`, `--nmap-host-timeout` shell out to `nmap`. Port scanning is frequently out of scope and is the single easiest way to get a report rejected or an account banned — confirm it is permitted in writing first.
- **`nuclei` / `ffuf`** — `--nuclei` (with `--nuclei-templates`, `--nuclei-severity`, `--nuclei-tags`, `--nuclei-rate-limit`, `--nuclei-timeout`, …) and `--ffuf` (with `--ffuf-wordlist`, `--ffuf-threads` default 40, `--ffuf-recursion`, …) drive those scanners against live hosts. These are loud, high-volume, and squarely active — treat them as a separate engagement with their own authorization.

If passive recon is all you are cleared for, stop at Phase 5 and hand `live.txt` / `cname-map.txt` to whatever manual testing the program permits.

## Source coverage: free tier vs keyed reality

The headline "55 passive sources" is the single most misleading number for a new user, and understanding the breakdown is what separates a wide inventory from a default-config duplicate. The README marks each source: `**` = an API token is **required** (silent until configured), `*` = token optional (works without, more with), no marker = free and queried out of the box.

**Free, no token (queried by default — ~13 sources):** AnubisDB, Arquivo.pt, CommonCrawl, crt.name, crt.sh (database mirror), Maltiverse, Mnemonic PassiveDNS, Subdomain Center, Sublist3r, Threatminer, UK Web Archive, Urlscan.io, Wayback Machine. Facebook CT is marked `**` but findomain ships a built-in token (since 0.2.4), so in practice it works without you doing anything.

**Optional token (`*`):** CertSpotter, HackerTarget — usable unauthenticated, higher limits with a key.

**Token required (`**`) — the ~40 you must configure to earn:** 360 PassiveDNS, Ahrefs, AlienVault OTX, BeVigil, BinaryEdge, BufferOver (free and paid tiers), BuiltWith, C99, Censys, Chaos, CIRCL PassiveDNS, Deepinfo, Detectify, DigiCert CertCentral, DNSlytics, DNSRepo, Farsight DNSDB, FOFA, FullHunt, Hunter.io, IntelX, LeakIX, Netlas, ONYPHE, PassiveTotal, Pentest-Tools, PublicWWW, Pulsedive, Quake, SecurityTrails, Shodan, SOCRadar, Spamhaus PassiveDNS, ThreatBook, VirusTotal, WhoisXMLAPI, ZETAlytics, ZoomEye.

The practical takeaway for a hunter:

| Tier | Count | Effort | Coverage effect |
|---|---|---|---|
| Free (no token) | ~13 + Facebook CT | none | Identical to every default-config run — guaranteed-duplicate baseline |
| Free-to-register keyed | several of the `**` set (SecurityTrails, VirusTotal, Chaos, FullHunt, LeakIX, Netlas…) have free tiers | one-time signup | The cheapest real coverage edge — most hunters never do this |
| Paid keyed | Censys, Shodan, BinaryEdge, PassiveTotal, FOFA, Quake… | paid plans | Marginal extra breadth; rarely worth it solo until the free set is exhausted |

Prioritize the free-to-register keys first: they move you off the duplicate baseline for zero cost, and `findomain --list-keys` is the audit that tells you which you still have unset. When you later claim "findomain found more than the other reporters," this is the mechanism that makes it true — not a better tool, a better-keyed one.

## Integrating findomain into a recon pipeline

Because findomain emits one normalized, deduplicated, lowercase name per line to stdout, it drops cleanly into a Unix pipeline — the common pattern is findomain for discovery, then dedicated tools for probing, so you are not leaning on findomain's bundled active modules (which are the scope-risky ones from Phase 6).

```bash
# Discover -> HTTP-probe -> template-scan, each tool doing its own job.
# findomain stays passive; httpx/nuclei are the active steps you authorize separately.
findomain -t example.com -q \
  | sort -u \
  | httpx -silent \
  > live-urls.txt
# -> findomain (passive) feeds httpx (active liveness); keep findomain's own
#    --http-status/--nuclei/--pscan OFF so the active surface is one tool you control.
```

`-q` / `--quiet` strips findomain's informational chatter so only subdomains reach the pipe (fatal errors and the "not found" message still show). `--stdin` lets findomain sit *downstream* of another discovery tool, reading targets from the pipe instead of `-f`/`-t`, and `--randomize` shuffles targets read from files so a multi-apex run does not hammer one zone's resolvers in a burst. For a repeatable per-program configuration, persist your flag choices in a config file rather than retyping them:

```bash
# findomain reads a config file (toml/json/ini/yml) named via -c / --config
findomain -c example-program.toml -f scope-apexes.txt
```

Config files let you pin source timeouts, excluded sources, resolver lists, and (for monitoring) database and webhook settings per program, so the only thing that varies between runs is the target list — which also keeps secrets out of your shell history. Treat the config file as a secret (it can hold Postgres credentials and webhook URLs) and keep it out of reports and version control.

## Where the money is with findomain

Enumeration itself is never the payout; the column that matters is **what the discovered host leads to**. Dupe risk on the raw name list is effectively total — rank by the triage that follows, not the discovery.

| Priority | Finding class | findomain feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Subdomain takeover (dangling CNAME to unclaimed service) | `-r --cname --http-status`, then manual claim-check | High (sometimes Critical) | Medium — first monitor to catch a *new* dangling record wins |
| 2 | Newly-created host (fresh staging/app, un-hardened) | `-m -j <prog>` + webhook + cron; `--query-jobname` diff | Varies by host — first-mover advantage | Low if you are genuinely first; high once announced |
| 3 | Forgotten dev/staging/admin host (debug panels, default creds, exposed APIs) | Wide passive sweep with keyed sources configured (Phase 0–1) | Medium–High (depends on the host) | High on the *name*, low on the *vuln* you find there |
| 4 | Exposed service on a non-standard port | `--pscan` / `--nmap-*` (active — authorization required) | Medium–High | Medium |
| 5 | Scope expansion / previously-unmapped asset feeding other bugs | Keyed-source breadth others lack; `--import-subdomains` union | Enabler (raises severity of the real bug) | Low — unique coverage is your edge |
| 6 | Information in CT/passive records (internal naming, pre-release product names) | Default CT + passive sources; `--query-database` history | Low–Info | High |

The pattern: rows 1–2 are where findomain *directly* produces or front-runs a payout, and both depend on features (CNAME tracking, monitoring diffs) that the default `findomain -t domain` invocation does not use. Rows 3–5 are enablers — findomain hands you the host, you find the bug. Report a bare subdomain from row 6 and expect it closed as Informative.

## Command recipes

Passive-first, low-volume settings called out inline. Replace `example.com` and `198.51.100.0/24` with your authorized scope.

```bash
# 1. Fast passive pass — free sources only, archives skipped, tight budgets
findomain -t example.com --exclude-sources wayback,commoncrawl \
  --source-budget 120 --source-timeout 15 -u quick.txt
# -> ~15-20s; one deduped name per line in quick.txt. No target traffic — pure API queries.

# 2. Full-breadth passive pass — after configuring keyed sources in Phase 0
findomain --list-keys          # confirm the high-signal sources show "set" first
findomain -t example.com -u wide.txt
# -> noticeably larger than a default-config run; the delta is your keyed-source edge.

# 3. Resolve + liveness, no brute force (stays low-volume)
findomain -t example.com -r --http-status --http-timeout 5 -u live.txt
# -> resolvable names annotated with HTTP status; dead passive ghosts dropped.
#    DNS/HTTP traffic only to names that already exist — not a flood.

# 4. Takeover triage map
findomain -t example.com -r --cname --http-status -u cname-map.txt
# -> name -> CNAME target + status. Grep CNAMEs pointing at SaaS/CDN providers,
#    then manually confirm "unclaimed" bodies (NoSuchBucket, GitHub 404, ...).

# 5. Merge subfinder/amass, resolve the union once, findomain adds nothing of its own
findomain -t example.com --import-subdomains subfinder.txt amass.txt -n -r -u merged.txt
# -> single clean resolved file; -n makes findomain a pure resolver/deduper.

# 6. Scope hygiene — keep in-scope, drop an excluded acquisition
findomain -t example.com --filter example.com --exclude acq-brand.example.com -u in-scope.txt

# 7. Continuous monitoring with Slack alerts on NEW names only
export findomain_slack_webhook="https://hooks.slack.example/..."
findomain -m -f scope-apexes.txt -j example-program --http-status --quiet
# -> stores to findomain.db (or Postgres with --postgres-*); webhook fires only on new finds.

# 8. Pull monitoring history without re-enumerating
findomain --query-jobname -j example-program
findomain --query-database -t example.com

# 9. Accuracy mode — custom resolvers + double-check to cut wildcard false positives
findomain -t example.com -r --resolvers my-resolvers.txt --double-dns-check -u clean.txt

# --- ACTIVE, authorization-required (NOT passive; can breach scope / disrupt) ---
# 10. Brute force under the apex (generates DNS volume — keep the wordlist sane)
findomain -t example.com -w subdomains.txt -r --rate-limit 5 -u brute.txt
# 11. Port scan live hosts via nmap (frequently OUT OF SCOPE — confirm in writing)
findomain -t example.com -r --pscan --iport 1 --lport 1000
```

Low-volume discipline, concretely: prefer recipes 1–9 (passive + resolution only) for any program where active scanning is not explicitly cleared. `--rate-limit <seconds>` throttles per-target enumeration, `--lightweight-threads` caps resolution/HTTP concurrency (default 50 — lower it on fragile targets), and the `--source-budget` / `--source-timeout` / `--archive-budget` triad bounds how hard and how long findomain hammers the *sources* (not the target). The brute-force (`-w`), permutation (`--permutations`), screenshot (`-s`), port-scan (`--pscan`), `--nuclei`, and `--ffuf` modules are the only ones that touch the target at volume — gate them behind explicit authorization.

## Pitfalls, noise and scope discipline

How hunters turn a harmless recon tool into a rejected report or a banned account:

- **Reporting the enumeration output as a finding.** A subdomain list is not a vulnerability. "I found `dev.example.com`" is an Informative close and, repeated, looks like noise farming. Always carry a discovery through to an actual issue (takeover, exposed panel, auth bypass) before you file.
- **Running active modules on a passive-only program.** `--pscan`/`--nmap-*`, `--nuclei`, `--ffuf`, and `-w` brute force send real, high-volume traffic to the target. Port scanning and fuzzing are out of scope on many programs and are a classic ban trigger. The default `findomain -t domain` is passive and safe; the moment you add those flags you are doing active testing and need matching authorization.
- **Enumerating out-of-scope names and testing them.** Passive sources return everything they have ever seen for an apex, including acquired brands, sibling companies, and explicitly-excluded hosts. Enumerating them is harmless; *testing* them is a scope violation. Enforce scope at the tool with `--filter` / `--exclude` and re-check the resulting list against the program's in-scope definition before any Phase 2+ probing.
- **Taking over a subdomain you were only meant to report.** Actually claiming a dangling service changes state under the target's domain and can disrupt the real owner. Unless the program explicitly permits proof-of-takeover, prove the dangling record (CNAME + provider "unclaimed" response) and stop.
- **Brute-force / permutation DNS floods.** `-w` with a massive wordlist, or `--permutations` on a large discovered set, can fire tens of thousands of DNS queries. That is disruptive and attributable. Use modest wordlists, `--rate-limit`, and conservative `--lightweight-threads`.
- **Wildcard DNS false positives.** Zones with wildcard records make every brute-forced name "resolve," producing a garbage list. Use `--double-dns-check` with custom `--resolvers`, and treat a run where *everything* resolves as a wildcard artifact, not a jackpot.
- **Leaking your API keys / webhook URLs.** Keys live in environment variables and config files; webhook URLs are secrets too. Do not paste `--list-keys` output, config files, or webhook URLs into reports or screenshots. `--list-keys` is designed to never print values — keep it that way in your own evidence.
- **Trusting "55 sources" at face value.** Out of the box you get only the no-token sources — roughly a quarter of the advertised breadth, identical to every other default-config hunter. If you did not configure keys (Phase 0), your "findomain found everything" claim is false and your coverage is a guaranteed duplicate.
- **Treating monitoring history as fresh each run.** Monitoring mode's whole point is the *diff*. If you point it at a new/empty database it will alert on the entire existing surface as "new." Seed history with `--no-monitor` first, or expect one noisy baseline run before the diffs become meaningful.

## Reporting evidence

For a takeover or exposed-host finding, capture:

1. **The discovery path, reproducibly.** The exact findomain command (minus secrets), its version, and the date. For takeovers, the `--cname`/`--http-status` line for the name, plus an independent `dig CNAME sub.example.com +short` and the raw provider response (`curl -sI https://sub.example.com`) showing the unclaimed/`NoSuchBucket`/404 body. The independent `dig`/`curl` matters — triagers trust primitives over tool output.
2. **Proof the record is dangling, not just unused.** CNAME target that points at a third-party provider, the provider's "unclaimed" response, and (if the program allows) a claimed resource serving your benign marker. If claiming is not permitted, state that explicitly and rely on the provider-404 evidence.
3. **Scope provenance.** Which in-scope apex the name derives from, so the triager can confirm it is in scope without guessing. If you used `--filter`/`--exclude`, note the scope rules you applied.
4. **For monitoring-sourced finds, the timeline.** The webhook alert timestamp and the `--query-jobname` history showing the name was absent before date X and present after — this substantiates "newly introduced" and supports a first-reporter claim.
5. **Impact framing, not a name dump.** "Dangling CNAME `assets.example.com → bucket.s3.example-cloud` is claimable, allowing arbitrary content under the `example.com` origin (cookie theft / phishing / CSP bypass)" beats "found a subdomain." Never attach the full enumeration file as "evidence"; attach only the one or few lines the finding needs.
6. **Redaction and cleanup.** Strip API keys, webhook URLs, and any out-of-scope names from every artifact. If you claimed a takeover target with permission, release it and note that you did.

## Flag quick reference

Verified against the clap `Cli` definitions in `src/cli.rs`. Grouped by role; passive/safe flags first, active/scope-risky flags marked.

**Targets and input**

| Flag | Meaning |
|---|---|
| `-t, --target <host>` | Single target apex |
| `-f, --file <file>` | Targets from a file, one per line |
| `--stdin` | Read targets from stdin (pipeline downstream position) |
| `--randomize` | Shuffle targets read from files |
| `--import-subdomains <files…>` | Merge external tool output into the run |
| `-n, --no-discover` | Do not enumerate; resolve/dedupe imported names only |

**Output and normalization**

| Flag | Meaning |
|---|---|
| `-o, --output` | Write to auto-named `<target>.txt` (boolean) |
| `-u, --unique-output <file>` | Write all results to one named file (conflicts with `-o`) |
| `-q, --quiet` | Suppress info messages (clean stdout for pipes) |
| `-v, --verbose` | Verbose/debug output |
| `--filter <str>` / `--exclude <str>` | Keep / drop names containing a string (scope control) |

**Resolution (low-volume, touches DNS/HTTP of existing names)**

| Flag | Meaning |
|---|---|
| `-r, --resolved` | Only resolvable names (mutually exclusive with `-i`) |
| `-i, --ip` | Names plus their IP addresses |
| `--resolvers <file>` | Custom resolver list (default Google/Cloudflare/Quad9) |
| `--lightweight-threads <n>` | Resolution/HTTP concurrency (default 50; `--threads` is the deprecated alias) |
| `--resolver-timeout <s>` | Resolver timeout (default 3) |
| `--double-dns-check` | Re-check hits to cut wildcard false positives (needs `--resolvers`) |
| `--no-wildcards` | Disable wildcard detection |
| `--enable-dot` | DNS over TLS |
| `--ipv6-only` | IPv6 lookups only |
| `--http-status` | Check HTTP status (`--http-timeout` 5, `--http-retries` 2, `--max-http-redirects` 0) |
| `--cname` | Track each resolved name's CNAME (takeover triage) |

**Source budgeting (bounds the sources, not the target)**

| Flag | Meaning |
|---|---|
| `--exclude-sources <list>` | Skip named sources (e.g. `wayback,commoncrawl`) |
| `--source-timeout <s>` | Per-request source timeout (default 30) |
| `--source-budget <s>` | Whole-search cap (default 300; `0` = unlimited) |
| `--archive-budget <s>` | Archive-index cap (default 20; `0` = fall under source budget) |
| `--rate-limit <s>` | Per-target enumeration rate limit |
| `--list-keys` | Audit which keyed sources are configured (values never printed) |
| `-c, --config <file>` | Config file (toml/json/ini/yml) |

**Monitoring and database**

| Flag | Meaning |
|---|---|
| `-m, --monitoring-flag` | Monitoring mode — alert on new names only |
| `-j, --jobname <name>` | Group a program's targets under one job |
| `--query-database` / `--query-jobname` | Read stored history without re-enumerating |
| `--no-monitor` | Save to DB without alerting (seed history) |
| `--reset-database` | Wipe the database (destructive) |
| `--sqlite <file>` | SQLite store (default `findomain.db`) |
| `--postgres-host/-user/-password/-port/-database` | Switch storage to PostgreSQL |
| `--webhook-max-wait <s>` | Back-off on webhook rate limits (default 0 = wait) |
| `--aempty` | Send alert even when nothing new was found |

**Active modules — authorization required, send traffic to the target**

| Flag | Meaning |
|---|---|
| `-w, --wordlist <file>` | Enable brute-force mode with a wordlist |
| `--permutations` / `--permutations-wordlist <file>` | Derive and resolve extra candidate names |
| `-s, --screenshots <dir>` | Screenshot live hosts (`--screenshots-threads` 10, `--sandbox`) |
| `--pscan` + `--iport`/`--lport` | Port scan (default 1–1000) via `nmap` |
| `--nmap-full` / `--nmap-syn` / `--min-rate` / `--nmap-arg` | nmap scan tuning (`--nmap-syn` needs root) |
| `--nuclei` + `--nuclei-templates`/`--nuclei-severity`/`--nuclei-tags`/… | Run nuclei against live hosts |
| `--ffuf` + `--ffuf-wordlist`/`--ffuf-threads`/`--ffuf-recursion`/… | Run ffuf against live hosts |
| `-x, --as-resolver` | Use findomain purely as a resolver for a file of names |
| `--smtp-server`/`--smtp-user`/`--email-to`/… | Email the report |

## References

- Findomain repository (README, source of the source list and feature claims) — https://github.com/Findomain/Findomain
- Findomain CLI definitions (clap `Cli` struct — authoritative flag names and defaults) — https://raw.githubusercontent.com/Findomain/Findomain/master/src/cli.rs
- Findomain installation & usage notes (monitoring, PostgreSQL/SQLite, webhook env vars) — https://raw.githubusercontent.com/Findomain/Findomain/master/docs/INSTALLATION.md
- Findomain wiki (features overview, runtime dependencies: PostgreSQL for monitoring, Chrome/Chromium for screenshots) — https://github.com/Findomain/Findomain/wiki
- OWASP subdomain takeover reference — https://owasp.org/www-community/attacks/Subdomain_takeover

*Research supplement added 2026-11-16, grounded in the Findomain repository README, docs/INSTALLATION.md, and the clap CLI definitions in src/cli.rs (github.com/Findomain/Findomain); every flag and environment variable cited above was verified against those sources (model: claude-opus-4-8).*
