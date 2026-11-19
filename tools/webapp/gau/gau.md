---
name: "gau"
categories:
  - blackarch-webapp
version: "180.4eabace"
homepage: "https://github.com/lc/gau/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "lc/gau"
github-stars: 5156
github-pushed: "2026-10-12"
github-archived: false
github-issues: 30
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# gau

> Fetches all known URLs for a domain from public archives: Wayback Machine, Common Crawl, AlienVault OTX and URLScan.

## What it's for

getallurls (gau, by Corben "lc" Leo) pulls every historically-recorded URL for a domain from AlienVault's Open Threat Exchange, the Wayback Machine, Common Crawl and URLScan — the union of what the archives ever saw, including long-dead endpoints, old parameters and forgotten JavaScript. It is the standard first step of external attack-surface enumeration: pipe its output into probing tools to find obsolete API routes, backup files and juicy paths nobody remembers. It fetches records, it does not probe: many returned URLs may no longer exist.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Pull every archived URL for a domain

Get the full historical picture.

```
gau example.com
```

Outcome: streams all known URLs for the domain from the default providers to stdout, one per line (multiple domains can be passed as arguments).

### Process many domains from stdin

Feed it a recon domain list.

```
cat domains.txt | gau --threads 5
```

Outcome: reads domains line by line from stdin (usable with or instead of arguments) with 5 concurrent workers, merging every domain's results into one output stream.

### Save and filter output

Produce a clean list for live probing.

```
gau --o example-urls.txt --blacklist png,jpg,gif --fc 404,302 example.com
```

Outcome: results are written to `example-urls.txt`, skipping the blacklisted static-image extensions entirely and dropping archived responses with status 404/302 (`--fc`; `--mc` is the match-only counterpart, `--ft`/`--mt` filter mime-types).

### Narrow by provider, date range and scope

Bound the query for large targets.

```
gau --providers wayback,otx --from 202201 --to 202412 --subs example.com
```

Outcome: restricts fetching to the Wayback and OTX providers, limits results to URLs captured between the given months (YYYYMM), and includes subdomains (`--subs`) — a bounded historical view instead of the firehose.

## Bug bounty workflow with gau

The single most important fact about gau on an authorized program: **it is a passive tool.** It never touches the target. Every URL it returns comes from a third-party archive — AlienVault OTX, the Wayback Machine (its CDX API), Common Crawl's index, or URLScan — which recorded the URL at some point in the past. That has three consequences that shape the whole workflow:

1. **gau generates zero traffic against the scope.** Running it cannot violate a rate limit, cannot trip a WAF, cannot DoS the target. The volume risk lives entirely in what you do with the output — the moment you feed its list into httpx, ffuf, dalfox, or nuclei, you are sending live requests, and *that* is where scope and rate discipline apply.
2. **Everything it returns may be dead.** Archived ≠ live. A URL captured in 2019 may 404 today, may belong to a decommissioned host, or may now point at a different app. gau fetches records; it does not confirm they still exist. Treat the raw output as leads, not findings.
3. **The archives can rate-limit *you*.** The providers throttle aggressive clients. High `--threads` across many domains, or looping gau in a tight shell, earns HTTP 429s and empty results from Common Crawl and URLScan — a self-inflicted outage of your own recon, never a target impact.

### Phase 0 — Scope the harvest before you run it

gau's passivity does not make its output automatically in-scope. The archives store everything they ever saw for a registered domain, including sibling subdomains, third-party CDNs, tracking pixels, and hosts the program explicitly excludes. Decide your scope boundary first:

```bash
# program scope expressed as a grep-able allowlist (in-scope apex + wildcards)
printf '%s\n' 'example.com' 'api.example.com' '.example.com' > scope.txt
```

Set up a config so you are not re-typing flags, and so URLScan uses your API key (anonymous URLScan access is heavily rate-limited and returns a fraction of the records). gau reads `~/.gau.toml` automatically; `--config` points at an alternate file.

```toml
# ~/.gau.toml
threads   = 5
verbose   = false
retries   = 15
subdomains = false
parameters = false
providers = ["wayback","commoncrawl","otx","urlscan"]
blacklist = ["ttf","woff","woff2","svg","png","jpg","jpeg","gif","ico","css"]
json      = false

[urlscan]
apikey = ""          # paste your urlscan.io key here — raises limits and record count

[filters]
from = ""
to   = ""
matchstatuscodes  = []
matchmimetypes    = []
filterstatuscodes = []
filtermimetypes   = ["image/png", "image/jpeg", "image/svg+xml"]
```

CLI flags override the config file, so the config holds your sane defaults and the command line holds the per-target tweaks.

### Phase 1 — Broad passive harvest

Pull the union of everything the archives remember for the scope. Start wide, with subdomains on, and let the providers run in parallel:

```bash
gau --subs --threads 5 --o raw-urls.txt example.com
```

For a list of in-scope apexes (common when a program lists several roots), feed them on stdin — gau merges every domain's results into one stream:

```bash
cat apexes.txt | gau --subs --threads 5 --o raw-urls.txt
```

`--subs` is the switch that turns a single-host query into an attack-surface sweep: it returns URLs for every subdomain the archives ever indexed under the apex, which routinely surfaces hosts that no live subdomain-enumeration tool finds anymore because they are offline (but their old endpoints and parameters are still instructive). This is gau's highest-leverage flag for bounty recon.

### Phase 2 — Normalize, scope-filter, deduplicate

The raw firehose is enormous and noisy. Three cheap passes make it usable without touching the target:

```bash
# 1. keep only in-scope hosts (strip third-party CDNs, out-of-scope subs)
grep -E '://([a-z0-9.-]+\.)?example\.com(/|:|$)' raw-urls.txt > scoped.txt

# 2. collapse URLs that differ only by parameter *values* (uro or urldedupe)
#    e.g. /item?id=1, /item?id=2, /item?id=3  ->  /item?id=1
uro -i scoped.txt -o deduped.txt

# 3. sort unique as a final guard
sort -u deduped.txt -o deduped.txt
```

gau has a built-in dedupe primitive too: `--fp` ("remove different parameters of the same endpoint") collapses URLs down to one representative per host+path, discarding the query string entirely — useful for an endpoint inventory, but it throws away the parameter *names* you need for injection mining, so run it as a *separate* pass, not on your parameter corpus.

```bash
gau --subs --fp --o endpoints.txt example.com     # bare endpoint list, no params
```

### Phase 3 — Triage by value (still passive)

Mine the normalized list for the four things archives are uniquely good at surfacing — all before sending a single live request:

```bash
# (a) sensitive/backup extensions — forgotten files the live site never links
grep -iE '\.(sql|bak|old|zip|tar\.gz|tgz|rar|7z|db|sqlite|log|conf|config|ini|env|yml|yaml|json|xml|pem|key|p12|war|jar|swp)(\?|$)' deduped.txt

# (b) secrets already leaked into archived query strings
grep -iE '[?&](api[_-]?key|apikey|token|access_token|auth|secret|password|passwd|sig|signature|key)=' deduped.txt

# (c) parameter mining for injection classes, via gf
gf xss  < deduped.txt > gf-xss.txt
gf sqli < deduped.txt > gf-sqli.txt
gf ssrf < deduped.txt > gf-ssrf.txt
gf lfi  < deduped.txt > gf-lfi.txt
gf redirect < deduped.txt > gf-openredirect.txt
gf idor < deduped.txt > gf-idor.txt

# (d) old API versions and admin/debug prefixes
grep -iE '/(api/v[0-9]+|admin|internal|debug|actuator|graphql|swagger|openapi|\.git|\.svn)' deduped.txt
```

Point (b) deserves emphasis: archived URLs frequently contain **real, historically-leaked credentials** — an API key that was in a URL in 2020 may still be valid. Treat any such hit as sensitive: do not paste it into third-party services, validate it against the program's own scope only, and report it as an information-disclosure finding if it still works.

### Phase 4 — First contact: confirm liveness at low volume

Now, and only now, you touch the target. Resolve which archived URLs still exist with httpx, and keep the request rate program-polite:

```bash
httpx -l deduped.txt -silent -rl 20 -timeout 10 -mc 200,201,204,301,302,401,403 \
      -o live.txt
```

`-rl 20` caps httpx at 20 requests/second — adjust to the program's stated limit (many want ≤ 5–10 rps). This single step converts gau's historical list into a live, probeable surface and is the boundary where all rate/scope discipline begins.

### Phase 5 — Targeted active testing on the scoped, live subset

Feed only the filtered, live, in-scope subset into active scanners — never the raw firehose. Keep concurrency and rate low, and prefer passive-first scanner modes:

```bash
# reflected XSS on the gf-xss candidates, rate-limited, blind-xss off unless authorized
cat gf-xss.txt | httpx -silent -mc 200 | dalfox pipe --delay 300 --worker 10 --skip-bav

# nuclei on live hosts, capped severity and rate — never the full template set at full speed
nuclei -l live.txt -severity medium,high,critical -rate-limit 20 -c 10
```

Swap payloads into a single parameter at a time with qsreplace when you want a controlled, one-request-per-URL probe instead of a scanner's fan-out:

```bash
cat gf-ssrf.txt | qsreplace 'https://canary.example.com/ssrf' \
  | httpx -silent -rl 10 -mc 200   # watch your canary/interactsh listener for callbacks
```

### Phase 6 — Diff snapshots for forgotten and newly-shipped endpoints

The archive is a time series. Two snapshots taken weeks apart, diffed, surface endpoints that appeared (fresh, least-reviewed code) or that you missed. Date-bound each pull with `--from`/`--to` (YYYYMM) and diff:

```bash
gau --subs --from 202401 --to 202406 --o h1.txt example.com
gau --subs --from 202407 --to 202412 --o h2.txt example.com
comm -13 <(sort -u h1.txt) <(sort -u h2.txt) > newly-archived.txt
```

New rows in `newly-archived.txt` are URLs the archives first saw in the later window — a cheap early-warning feed for a target you revisit. The same `comm` move against a snapshot you saved last month tells you what shipped since.

## Where the money is with gau

gau's job is reconnaissance; the payouts come from what the recon *reveals*. Priorities, with the gau feature that reaches each class and the realistic dupe risk (archived-URL recon is heavily automated, so common classes dupe fast — your edge is thorough scope-filtering and chasing the long-tail hosts):

| Priority | Finding class | gau feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Leaked secrets in archived query strings (`apikey=`, `token=`) | Phase 3(b) grep over raw output | High–Critical | Medium — many hunters grep, few check validity |
| 2 | Forgotten/backup files (`.sql`, `.bak`, `.env`, `.git`) | Phase 3(a) extension grep | High–Critical | Medium |
| 3 | Injection via archived parameters (XSS/SQLi/SSRF/LFI) | `--subs` corpus → `gf` → dalfox/nuclei | Medium–High | High — the single most automated gau pipeline |
| 4 | Deprecated API versions / endpoints still live | `--fp` endpoint list, `/api/v*` grep | Medium–High | Medium |
| 5 | Offline-subdomain endpoints that moved, not died | `--subs` (archives remember dead hosts) | Varies | Low — the long tail others skip |
| 6 | Open redirect / SSRF via archived redirect params | `gf redirect`, `gf ssrf` | Medium | High |
| 7 | IDOR candidates (object-id params in old URLs) | `gf idor`, param mining | Medium–High | Medium |
| 8 | Exposed debug/admin/actuator/swagger surfaces | prefix grep over output | Medium–High | Medium |
| 9 | Pure endpoint inventory feeding manual testing | `--fp`, `--o` | Info (enabler) | — |

The two rows with the best risk/reward are #1 and #5: leaked secrets because few hunters bother to *validate* the key (they just report its presence, which triagers reject as theoretical), and offline-subdomain endpoints because they are the long tail that the `gau | gf | dalfox` crowd never reaches — those archived hosts don't resolve, so the automation drops them, but their endpoint patterns and parameter names often still apply to the live app.

## Command recipes

Each block pairs a command with what you should see and the low-volume setting that keeps it program-safe. Flag names are verified against the lc/gau v2 documentation.

```bash
# Baseline passive sweep — zero target traffic, subdomains included
gau --subs --threads 5 --o raw.txt example.com
#   -> raw.txt: one URL per line, union of all four providers. Target sees nothing.
#      Keep --threads modest (5) so the *archives* don't 429 you.
```

```bash
# Provider selection — drop the slow/noisy ones for a fast first pass
gau --providers wayback,otx --subs example.com
#   -> only Wayback + OTX. Common Crawl is the slowest and most rate-limited;
#      skipping it gives a quicker, thinner result. Valid names:
#      wayback, commoncrawl, otx, urlscan (exact spellings).
```

```bash
# Static-asset suppression at the source
gau --subs --blacklist png,jpg,jpeg,gif,svg,ico,css,woff,woff2,ttf example.com
#   -> drops those extensions before writing. --blacklist takes bare
#      extensions (no dot). Shrinks the corpus to app URLs worth testing.
```

```bash
# Filter by what the ARCHIVE recorded (not a live re-request)
gau --subs --mc 200 --mt text/html,application/json example.com
#   -> --mc keeps only records the archive stored with status 200; --mt keeps
#      only those mime-types. --fc / --ft are the exclude counterparts.
#      NOTE: these match the archive's stored metadata (e.g. Wayback CDX
#      statuscode/mimetype), NOT a fresh probe — a 200 here does not mean
#      the URL is live today. Confirm liveness with httpx (Phase 4).
```

```bash
# Date-bounded pull for large targets / snapshot diffing
gau --subs --from 202401 --to 202412 example.com
#   -> only captures timestamped within 2024 (YYYYMM format). Bounds the
#      firehose on huge domains and powers the Phase 6 diff.
```

```bash
# Machine-readable output for a pipeline
gau --subs --json --o urls.ndjson example.com
#   -> newline-delimited JSON, ONE field per record: {"url":"https://..."}.
#      There is no provider/status/mime field in gau's JSON — it is just the
#      URL wrapped. Consume with: jq -r '.url' urls.ndjson
```

```bash
# Endpoint inventory (parameters stripped and collapsed)
gau --subs --fp --o endpoints.txt example.com
#   -> one representative per host+path, query strings removed. Good for a
#      route map; run SEPARATELY from parameter mining (it discards params).
```

```bash
# Route everything through Burp for logging/inspection
gau --subs --proxy http://127.0.0.1:8080 example.com
#   -> gau's archive requests flow through the proxy (socks5:// or http://).
#      Useful to audit exactly which archive endpoints gau hits.
```

```bash
# Robustness knobs for flaky archive APIs
gau --subs --retries 5 --timeout 60 example.com
#   -> --retries re-attempts failed archive HTTP calls; --timeout (seconds)
#      bounds each. Raise both when Common Crawl/URLScan are slow, rather
#      than raising --threads (which makes rate-limiting worse).
```

## Pitfalls, noise and scope discipline

gau rarely gets a *hunter* banned — it doesn't touch the target — but it is the front of a pipeline that absolutely can. The failure modes:

- **Feeding the raw firehose into an active scanner.** The classic self-own: `gau --subs example.com | nuclei` or `| ffuf` with no scope filter and no rate limit. You blast thousands of archived URLs — many out-of-scope, many dead — at full speed, tripping the WAF and blowing the program's rate limit. Always filter to scope (Phase 2) and confirm liveness at low rate (Phase 4) *before* any scanner, and cap the scanner's rate (`-rate-limit`, `-rl`, `--delay`).
- **Out-of-scope hosts in the output.** `--subs` and the archives themselves pull sibling subdomains, third-party CDNs, and hosts the program excludes. Probing those is a scope violation even though gau's own fetch was passive. Grep to the program's explicit scope before Phase 4 — see Phase 2.
- **Treating archived URLs as live findings.** Reporting "endpoint X exists" straight from gau, without confirming it still responds, gets reports closed as not-reproducible. gau proves the URL *was* seen, not that it *is* there. Always attach a live request/response.
- **Mishandling leaked secrets.** Archived query strings contain real tokens. Do not paste them into online decoders, pastebins, or third-party "is this key valid" services — that is itself a disclosure. Validate only against the in-scope target, capture minimal proof, and report.
- **Hammering the archives.** High `--threads` over a long apex list, or gau in a tight loop, gets *you* rate-limited (429s, truncated results) by Common Crawl and URLScan — degrading your own recon. Keep `--threads` ≈ 5, set a URLScan API key to raise your own limits, and prefer `--retries`/`--timeout` over more threads for reliability.
- **The ohmyzsh alias collision.** The oh-my-zsh `git` plugin aliases `gau` to `git add --update`, so `gau example.com` silently runs git instead of the tool. If gau "does nothing" or errors about git, that's why — call it by full path or `unalias gau` (upstream issue #8).
- **Stale, incomplete coverage.** gau is only as good as what the archives captured. A brand-new host, or one with `robots.txt`/`noarchive` history, may be nearly invisible. gau complements — never replaces — live crawling (katana, hakrawler) and subdomain enumeration.

## Reporting evidence

gau is a recon enabler, so what goes in the report is almost never "gau output" — it is the validated finding gau *led you to*, plus a reproducible trail from archive to live proof:

- **The exact gau command**, including `--providers`, `--subs`, and any `--from`/`--to`, so the triager can reproduce the discovery. State the run date — archive contents change over time.
- **The specific archived URL** that surfaced the lead, and ideally its provenance (which provider / the Wayback timestamp), so the triager can see it in the archive independently: e.g. `http://web.archive.org/web/2020*/https://example.com/old-endpoint?apikey=...`.
- **The live confirmation** — the httpx or curl request/response pair proving the endpoint still exists today, with status, relevant headers, and a redacted body snippet. This is the evidence that converts a historical lead into a valid finding.
- **For leaked-secret findings:** proof the credential still works against the in-scope target (minimal — one authenticated call), with the secret redacted in the report body, plus where in the archive it leaked.
- **For injection/IDOR findings:** the `gf`-matched parameter, the payload, and the before/after responses — standard PoC discipline from the downstream tool (dalfox/nuclei), not gau itself.
- **Scope statement:** note explicitly that gau is passive and generated no target traffic, and that all active probing stayed within the program's stated scope and rate — pre-empts the "how much traffic did you send" question.

## References

- gau (lc/gau) — official repository and README — https://github.com/lc/gau
- gau README (raw) — flags table, config file, providers, install — https://raw.githubusercontent.com/lc/gau/master/README.md
- gau example config (`.gau.toml`) — https://github.com/lc/gau/blob/master/.gau.toml
- gau JSON output struct (`pkg/output/output.go`, `JSONResult{Url string json:"url"}`) — https://raw.githubusercontent.com/lc/gau/master/pkg/output/output.go
- gau output dispatch (`cmd/gau/main.go`) — https://raw.githubusercontent.com/lc/gau/master/cmd/gau/main.go
- gau ohmyzsh alias collision (issue #8) — https://github.com/lc/gau/issues/8
- gf (tomnomnom) — pattern matching for parameter mining — https://github.com/tomnomnom/gf
- qsreplace (tomnomnom) — query-string payload substitution — https://github.com/tomnomnom/qsreplace
- uro — URL deduplication/normalization — https://github.com/s0md3v/uro
- httpx (ProjectDiscovery) — liveness/probe with rate limiting — https://github.com/projectdiscovery/httpx
- dalfox — XSS scanner (`pipe` mode) — https://github.com/hahwul/dalfox
- nuclei (ProjectDiscovery) — templated scanning with `-rate-limit` — https://github.com/projectdiscovery/nuclei

*Research supplement added 2026-11-16, grounded in the lc/gau repository, README, `.gau.toml`, and verified source (`pkg/output/output.go`, `cmd/gau/main.go`) at https://github.com/lc/gau (model: claude-opus-4-8).*
