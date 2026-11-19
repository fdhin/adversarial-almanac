---
name: "photon"
categories:
  - blackarch-webapp
version: "v1.2.2.r50.gc8d2566"
homepage: "https://github.com/s0md3v/Photon"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "s0md3v/Photon"
github-stars: 13328
github-pushed: "2026-10-17"
github-archived: false
github-issues: 60
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# photon

> Fast OSINT crawler that extracts URLs, intel, files, secret keys, and DNS data from a website.

## What it's for

Photon crawls a target site and sorts what it finds: in-scope and out-of-scope URLs, parameterized URLs, intel (emails, social media accounts, cloud buckets), document and media files, JavaScript files and the endpoints inside them, strings matching a custom regex, subdomains, and secret keys such as API keys and hashes. Results are written in an organized per-target output directory or exported as CSV/JSON. It can seed a crawl from archive.org, dump DNS data, and clone the site locally. Actively maintained Python 3.

## Typical use cases

### Crawl a site and organize findings

```
photon -u https://example.com
```

Outcome: crawls the site and writes categorized results — URLs, intel, files, JS endpoints — into an output directory named after the target.

### Clone the site for offline review

```
photon -u https://example.com --clone
```

Outcome: in addition to extraction, the crawled pages are saved locally so the site can be reviewed offline.

### Seed the crawl from web archives

```
photon -u https://example.com --wayback
```

Outcome: URLs archived by archive.org are fetched first and used as seeds, reaching pages no longer linked from the live site.

### Hunt secret keys

```
photon -u https://example.com --keys
```

Outcome: responses are scanned for auth/API keys and hashes, with findings listed alongside the pages that exposed them.

### Dump DNS and subdomain data

```
photon -u https://example.com --dns
```

Outcome: enumerates subdomains and collects DNS-related data for the target domain.

### Export results for tooling

```
photon -u https://example.com -e json --output example-results
```

Outcome: the crawl's findings are additionally exported as JSON into the named directory, ready to feed other tooling (CSV is also supported).

No corpus evidence — derived from upstream documentation.

---

## Bug bounty workflow with photon

Photon is a **link-extraction crawler**, not a vulnerability scanner. On an authorized program it earns its place in Phase 1 (attack-surface mapping): point it at an in-scope host, let it harvest URLs, parameterized endpoints, JavaScript files, the endpoints hidden inside that JS, intel (emails, social handles, cloud buckets), and high-entropy secrets — then feed the structured output into the tools that actually test things (gf, dalfox, nuclei, ffuf). Everything below assumes written permission, adherence to the program's rules of engagement, and low-volume settings.

A ground-truth note before the flags: the catalog `version` above is the packaging's `git describe` string, but the binary's own banner reports **v1.3.2**. Two consequences for this guide: **`--ninja` no longer exists** — it was removed in v1.3.0, so any blog or the pre-2019 wiki that tells you to route requests through third-party sites is stale — and **`-p/--proxy`** (HTTP/SOCKS5, rotating lists, or a file of proxies) was added in v1.3.1/v1.3.2. Every flag used below is verified against the current `photon.py` argparse block.

### Phase 0 — Passive-first seeding (no packets to the target yet)

Start from the archive, not the live site. `--wayback` pulls URLs that archive.org already holds and uses them as crawl seeds, so your initial URL universe costs the target nothing:

```
photon -u https://example.com --wayback -o example-recon
```

Photon only fetches archived URLs recorded within the current year "to make sure they aren't dead," so this is a focused seed set rather than the full historical dump you would get from `waybackurls`/`gau`. Combine both: feed a large historical list in through `--seeds` (below) and keep `--wayback` for the recent slice.

### Phase 1 — Scoped crawl at safe volume

The defaults are already conservative — **depth `-l 2`, threads `-t 2`, delay `-d 0`, timeout `--timeout 6`** — but on a bounty target you set them explicitly so your report can prove you were gentle:

```
photon -u https://example.com -l 3 -t 2 -d 1 --timeout 10 -o example-recon
```

- `-l/--level` is **crawl depth** (integer, default 2). Level 1 = homepage + seeds; each increment follows one more hop of in-scope links. Depth 3 is usually plenty; depth is exponential, so 4+ on a large site is a request flood.
- Photon crawls **only same-domain (`internal`) URLs**; off-domain links are recorded in `external` but never fetched, so a crawl stays in scope by construction (subject to `--exclude`, below).
- `-d/--delay` (float seconds) throttles between requests; `-t/--threads` (int) is concurrency. Keep threads at the default 2 on small/unknown infrastructure — the upstream docs explicitly warn that high thread counts "can trigger security mechanisms and bring down small sites."

### Phase 2 — Pull the structured output

A finished crawl writes one file per populated category into the output directory (defaults to the target host when `-o` is omitted). The category set is fixed in the source — `internal`, `external`, `fuzzable`, `endpoints`, `scripts`, `intel`, `robots`, `custom`, `failed`, `files`, `keys` (plus `subdomains` when `--dns` is used). The ones that matter for hunting:

| File | Contents | What you do with it |
|---|---|---|
| `fuzzable.txt` | URLs carrying query parameters | The raw material for gf/dalfox/sqlmap — this is the payout file |
| `endpoints.txt` | Paths/routes extracted from JavaScript | Hidden API routes; feed to ffuf and manual review |
| `scripts.txt` | JS file URLs | Download and re-mine with LinkFinder/xnLinkFinder for what Photon's regex missed |
| `intel.txt` | Emails, social accounts, cloud buckets | Bucket takeover / account-takeover leads; intel is tied to the URL it was found on (since v1.3.0) |
| `keys.txt` | High-entropy strings (API keys, hashes) | Verify each by hand — high false-positive rate |
| `internal.txt` | All in-scope URLs crawled | Master URL inventory; diff across scans to spot new routes |
| `robots.txt` | Disallowed paths pulled from the site's robots.txt | Free pointers to "interesting" directories |

### Phase 3 — Pipe into the testing tools

Photon's `--stdout=<category>` prints a single category to stdout so you can chain it live, and every saved `*.txt` can be `cat`'d into the same pipelines. The canonical parameter-bug pipeline:

```
# XSS candidates straight into dalfox
photon -u https://example.com -l 3 --stdout=fuzzable | gf xss | dalfox pipe

# or from the saved file, pattern by pattern
cat example-recon/fuzzable.txt | gf ssrf  | nuclei -tags ssrf
cat example-recon/fuzzable.txt | gf sqli  | sqlmap --batch -m -
cat example-recon/fuzzable.txt | gf redirect
```

`endpoints.txt` and `scripts.txt` feed discovery rather than injection:

```
# treat JS-derived endpoints as a wordlist against the live host
ffuf -w example-recon/endpoints.txt -u https://example.com/FUZZ -mc all -fc 404 -ac

# re-mine each JS file for endpoints Photon's regex missed
cat example-recon/scripts.txt | while read js; do python3 linkfinder.py -i "$js" -o cli; done
```

### Phase 4 — Validate findings by hand

Nothing Photon prints is a finding on its own. `keys.txt` entries are high-entropy guesses — confirm the string is a live credential (and never use it beyond proving validity). `fuzzable` hits are only leads until a single crafted request demonstrates the bug. `intel` buckets need an actual permission check. The crawler's job ends at "here is the attack surface"; the report is built from the one-request PoCs you run next.

## Where the money is with photon

Photon reaches a finding class by *surfacing the surface*; the severity comes from what you do with the surface afterward. Priorities below are ordered by how directly Photon contributes.

| Finding class | What Photon gives you | Typical severity | Dupe risk |
|---|---|---|---|
| Exposed secrets / API keys | `--keys` → `keys.txt` high-entropy hits; secrets embedded in `scripts.txt` JS | High–Critical | Medium — public JS is heavily scanned; be fast and verify liveness |
| Hidden API endpoints in JS | `endpoints.txt` (routes parsed from JavaScript) | Medium–High | Medium — depends on how deep the JS is mined vs. other hunters |
| Parameterized-URL bugs (XSS/SQLi/SSRF/open-redirect) | `fuzzable.txt` → gf/dalfox/sqlmap | Medium–High | High on reflected XSS; lower on chained SSRF |
| Subdomain / DNS attack surface | `--dns` → `subdomains.txt` + DNS graph | Info→High (if a dangling CNAME → takeover) | High — subdomain lists are commodity; the takeover is the prize |
| Cloud-bucket & intel exposure | `intel.txt` (buckets, emails, handles) | Medium–High (open/writable bucket) | Medium |
| Sensitive files / backups | `files.txt` (documents, media) and `robots.txt` disallows | Low–High (depends on content) | Medium |
| Forgotten/added routes over time | diff of `internal.txt` between scans | varies | Low — fresh routes are the least-reviewed code |

The highest-yield, lowest-dupe move is treating Photon as a **JS endpoint + secret miner** (`--keys`, `scripts.txt`, `endpoints.txt`) rather than as yet another URL spider — URL lists are commodity, but a live key or an undocumented admin route pulled from a bundle is not.

## Command recipes

Each block lists the safe setting in-line. Defaults (depth 2 / threads 2 / delay 0 / timeout 6) are already low-volume; the recipes make caps explicit so your evidence shows restraint.

```
# Baseline scoped crawl — explicit low volume, named output dir
photon -u https://example.com -l 2 -t 2 -d 1 --timeout 10 -o example-recon
#  -> example-recon/ populated with internal.txt, external.txt, fuzzable.txt,
#     endpoints.txt, scripts.txt, intel.txt, robots.txt, files.txt (empty files omitted)
```

```
# Secret hunt — scan responses + JS for high-entropy strings
photon -u https://example.com --keys -v -o example-recon
#  -> keys.txt lists candidate keys/hashes; -v streams finds as they appear.
#     EXPECT false positives (minified hashes, cache-busters) — verify each.
```

```
# JSON export for tooling hand-off
photon -u https://example.com -l 3 -t 2 -d 1 -e json -o example-recon
#  -> machine-readable export alongside the per-category .txt files (csv also supported)
```

```
# Authenticated crawl (session cookie) — stays in the logged-in surface
photon -u https://example.com -c "session=REDACTED" --user-agent "bugbounty-recon (hackerone.com/yourhandle)" -d 1 -o example-auth
#  -> crawls pages only reachable while authenticated. Use a THROWAWAY test account,
#     and EXCLUDE destructive links (see below) before running authenticated.
```

```
# Exclude dangerous / out-of-scope paths (regex) — avoid logout, delete, admin actions
photon -u https://example.com --exclude "logout|signout|/delete|/admin/.*(delete|purge)|\?action=" -d 1 -o example-recon
#  -> matching URLs are neither crawled nor shown. Critical when crawling authenticated.
```

```
# Add historical + manual seeds (nargs: space- or comma-separated)
photon -u https://example.com --wayback --seeds https://example.com/old-portal https://example.com/beta -o example-recon
#  -> expands the starting URL set beyond what is linked from the homepage
```

```
# Subdomain + DNS map
photon -u https://example.com --dns -o example-recon
#  -> subdomains.txt plus a rendered DNS/relationship graph image in the output dir
```

```
# Route through Burp / a logging proxy for evidence capture (v1.3.1+)
photon -u https://example.com -p http://127.0.0.1:8080 -t 1 -d 1 -o example-recon
#  -> every request flows through the proxy; -p also accepts socks5://host:port,
#     a rotating list, or a file of proxies (v1.3.2). Threads 1 keeps Burp readable.
```

```
# Pipe one category straight into the next tool, no intermediate file
photon -u https://example.com -l 3 --stdout=fuzzable | gf xss | dalfox pipe
photon -u https://example.com --stdout=endpoints | anew endpoints-all.txt
```

### gf pattern coverage for `fuzzable.txt`

`fuzzable.txt` is only as useful as the patterns you run against it. The standard gf pack maps parameter shapes to bug classes; run the set, not just `xss`:

```
F=example-recon/fuzzable.txt
cat $F | gf xss            > cand-xss.txt      # reflected/stored XSS params
cat $F | gf sqli           > cand-sqli.txt     # id=, select=, where= style params
cat $F | gf ssrf           > cand-ssrf.txt     # url=, dest=, redirect=, next=, callback=
cat $F | gf lfi            > cand-lfi.txt      # file=, path=, template=, include=
cat $F | gf redirect       > cand-redir.txt    # open-redirect candidates
cat $F | gf ssti           > cand-ssti.txt     # template-injection candidates
cat $F | gf idor           > cand-idor.txt     # id/uid/account params for two-account tests
cat $F | gf interestingparams
```

Then test each file with the matching tool at single-request volume — `dalfox file cand-xss.txt`, `nuclei -l cand-ssrf.txt -tags ssrf`, `sqlmap -m cand-sqli.txt --batch --level 1`. For blind XSS, give dalfox a collaborator so you catch out-of-band fires:

```
dalfox file cand-xss.txt -b https://YOURID.oast.site --waf-evasion
```

### Custom-regex mining with `-r` → `custom.txt`

`-r/--regex` extracts anything matching your pattern into `custom.txt` *as the crawl runs*, which turns Photon into a targeted grep over the whole site's responses and JS. High-value patterns:

```
# JWTs sitting in JS or responses
photon -u https://example.com -r "eyJ[A-Za-z0-9_-]+\.[A-Za-z0-9_-]+\.[A-Za-z0-9_-]+" -o example-recon

# S3 / GCS / Azure bucket references
photon -u https://example.com -r "[a-z0-9.-]+\.s3[.-][a-z0-9-]*\.amazonaws\.com|storage\.googleapis\.com/[a-z0-9._-]+|[a-z0-9-]+\.blob\.core\.windows\.net" -o example-recon

# internal hostnames / staging references leaking in bundles
photon -u https://example.com -r "https?://[a-z0-9.-]*(internal|staging|dev|corp|vpn)[a-z0-9.-]*" -o example-recon
```

`custom.txt` is the companion to `keys.txt`: use `--keys` for entropy-based secret discovery and `-r` when you already know the shape of what you're hunting.

### Subdomain output → takeover check

`subdomains.txt` from `--dns` feeds straight into a takeover pass — a dangling CNAME on an in-scope subdomain is where an info-level list becomes a real finding:

```
photon -u https://example.com --dns -o example-recon
cat example-recon/subdomains.txt | httpx -silent | subzy run --targets -   # or subjack
```

Only test subdomains that are themselves in scope; a wildcard-scope program is not a license to probe arbitrary siblings.

### Diffing scans to catch freshly shipped routes

The least-reviewed code on any target is the code that just shipped. Re-crawl on a cadence and diff `internal.txt`/`endpoints.txt` — new entries are the freshest attack surface and the lowest dupe risk:

```
photon -u https://example.com -l 3 -t 2 -d 1 -o scan-$(date +%F)
comm -13 <(sort scan-2026-11-09/endpoints.txt) <(sort scan-2026-11-16/endpoints.txt)
#  -> endpoints present this week but not last week: test these first
```

### Liveness-filter before you hammer

Photon's `internal`/`endpoints` output can contain dead or redirecting URLs. Filter with `httpx` so your follow-up tools only touch live surface (and so you send fewer wasted requests at the target):

```
cat example-recon/internal.txt | httpx -silent -mc 200,301,302,401,403 > live.txt
```

### Fitting Photon into a multi-host recon chain

On a wildcard program you rarely crawl one host — you enumerate subdomains first, filter to live ones, then crawl each. Photon is the per-host crawl step in that chain:

```
subfinder -d example.com -silent \
  | httpx -silent -mc 200,301,302 \
  | while read host; do
      photon -u "$host" -l 2 -t 2 -d 1 --timeout 10 -o "recon/$(echo "$host" | sed 's#https\?://##;s#/#_#g')"
    done
# then merge the per-host fuzzable sets for one gf/dalfox pass
cat recon/*/fuzzable.txt | sort -u | anew all-fuzzable.txt
```

Keep `-t 2 -d 1` inside the loop: you are now making many crawls back to back, and per-host politeness is what keeps the aggregate request rate inside program limits. Space the loop (`sleep`) if the program caps total RPS across the apex.

### Reading the secondary categories

The headline files are `fuzzable`/`endpoints`/`keys`, but the quieter categories carry leads too:

- **`external.txt`** — off-domain links. Not crawled, but a goldmine for pivoting: third-party integrations, forgotten vendor subdomains, and out-of-scope-but-related assets worth checking against the program's scope.
- **`robots.txt`** — the disallow list the site handed you; "please don't look here" is a directory hint, not a boundary.
- **`files.txt`** — documents and media (PDF, DOCX, XLSX, backups). Metadata and stray internal files surface here; grep for `.sql`, `.bak`, `.zip`, `.env`.
- **`failed.txt`** — URLs Photon couldn't fetch. A cluster of failures can mean WAF blocking (back off your threads) or auth-gated surface (re-run with `-c`).
- **`intel.txt`** — emails feed account-enumeration and password-reset tests; cloud buckets feed permission checks; social handles feed OSINT. Each intel item is tied to the URL it was found on since v1.3.0, so you always have provenance.

### Auth and header edge cases

Beyond `-c/--cookie` for a session, `--user-agent` accepts a comma-separated list that Photon rotates through requests — useful when a single static UA gets fingerprinted, and the place to put your identifying bug-bounty contact string:

```
photon -u https://example.com --user-agent "recon-yourhandle (h1),recon-yourhandle (alt)" -c "session=REDACTED" -d 1 -o example-auth
```

`--headers` enables supplying additional custom request headers for the crawl (for example a second auth header or an API version header the app requires). Pair it with `-c` when a session cookie alone isn't enough to stay authenticated.

### End-to-end wrapper (one host, program-safe defaults)

A single script that runs the phases in order and hands you ready-to-test files — adjust the rate knobs to the program's limits before running:

```bash
#!/usr/bin/env bash
# usage: ./recon.sh https://example.com
set -euo pipefail
TARGET="$1"
OUT="recon-$(echo "$TARGET" | sed 's#https\?://##;s#/##g')"
UA="recon-yourhandle (program contact)"

# Phase 0-2: passive-seeded, low-volume crawl + secret scan + DNS map
photon -u "$TARGET" --wayback -l 3 -t 2 -d 1 --timeout 10 \
       --keys --dns --user-agent "$UA" \
       --exclude "logout|signout|/delete|/purge|\?action=" \
       -e json -o "$OUT"

# Phase 3: liveness-filter, then split into bug-class candidate files
cat "$OUT/internal.txt"  | httpx -silent -mc 200,301,302,401,403 > "$OUT/live.txt"
for p in xss sqli ssrf lfi redirect idor ssti; do
  gf "$p" < "$OUT/fuzzable.txt" > "$OUT/cand-$p.txt" || true
done

# Phase 4 pointers (run by hand, single-request volume):
echo "[*] dalfox file $OUT/cand-xss.txt -b https://YOURID.oast.site"
echo "[*] nuclei -l $OUT/cand-ssrf.txt -tags ssrf"
echo "[*] verify each $OUT/keys.txt entry before reporting"
echo "[*] subzy run --targets $OUT/subdomains.txt   (in-scope subs only)"
```

Everything destructive stays manual: the script discovers and sorts; you pull the trigger on each candidate yourself.

### When katana (or a headless crawler) beats Photon

Photon fetches with a plain HTTP client and **does not execute JavaScript**. It finds endpoints by running regexes over static JS *source*, which is excellent for routes baked into a bundle but blind to anything assembled at runtime. On a modern SPA where the router, API base URL, and links are constructed in the DOM after hydration, Photon will return a near-empty `internal.txt` and a thin `endpoints.txt`. Reach for a headless/JS-aware crawler there:

- **`katana -u https://example.com -headless -jc`** renders with a real browser (`-headless`) and crawls JS (`-jc`), so it sees client-side routes Photon cannot. `-kf all` adds known-file probing.
- Use Photon when the target is server-rendered, link-heavy, or you specifically want its secret/intel/bucket extraction and tidy per-category files.
- Use katana `-headless` (or hakrawler + a renderer) for JS-heavy SPAs, infinite-scroll apps, and anything behind client-side routing.
- They are complementary: run both, then `anew`/`sort -u` the URL sets before piping into gf.

## Pitfalls, noise and scope discipline

**Volume is the fastest way to get banned.** The defaults are gentle (2 threads, no delay, depth 2), but the moment you raise `-t`, Photon becomes a burst crawler. Upstream warns high thread counts can trip security mechanisms and knock over small sites. Keep `-t 2`, add `-d 1` (or more), and cap depth at `-l 3`. If the program publishes a rate limit, honor it — Photon has no requests-per-second cap, only the delay/thread knobs.

**`--clone` is an evidence and data-handling trap.** It writes a full local copy of every crawled page. That (a) doubles the request cost because every page body is fetched and saved, (b) means you are now *retaining the target's content on disk*, which some programs' rules restrict, and (c) tempts you to attach the whole mirror to a report. Don't. **Evidence comes from the per-category output listings and the specific `*.txt` files, never from the cloned site.** Only clone when you have a concrete offline-analysis reason and the program permits content retention — and purge it afterward.

**Authenticated crawling needs `--exclude` first.** A logged-in crawl will happily follow `GET /logout`, `?action=delete&id=…`, state-changing links, and anything else a browser would. Build an `--exclude` regex for logout/delete/purge/admin-action patterns *before* you attach a session cookie, and use a throwaway test account so a stray state change can't harm real data.

**`keys.txt` is noisy — triage, don't submit raw.** High-entropy detection flags minified hashes, cache-busting tokens, Sentry DSNs, and public client IDs alongside real secrets. Every entry is a *candidate*; confirm it is a live, privileged credential before it goes in a report, and never exercise it beyond the minimum proof of validity.

**Stay in scope by construction, then double-check.** Photon only crawls same-domain URLs and merely records `external` ones, so it won't wander off-site on its own. But `--seeds` and `--wayback` can introduce out-of-scope hosts into the seed set — review seeds against the program's scope, and exclude siblings the program lists as out of scope.

**Don't trust stale docs.** The pre-2019 wiki documents `--ninja` (routing requests through third-party sites) — that mode was removed in v1.3.0 and routing a target's traffic through unrelated third parties would be an OPSEC and authorization problem anyway. The current tool is `requests`-based and direct; use `-p` if you need a proxy.

**SPA blind spot = false "clean" result.** A thin crawl on a JavaScript app is not evidence the surface is small — it's evidence Photon couldn't see it. Confirm with a headless crawler before concluding there's nothing there.

## Reporting evidence

Capture enough to prove both the finding and that you found it responsibly:

- **The exact command line**, flags included. Showing `-t 2 -d 1 --timeout 10` in the report demonstrates low-volume discipline to the triager.
- **Tool version** — the banner line (`v1.3.2`) — so the reader knows which feature set produced the output.
- **The relevant category file(s)**, not the whole output dir: `fuzzable.txt`, `endpoints.txt`, `keys.txt`, `intel.txt`, or `subdomains.txt` as applicable, quoted as snippets with the irrelevant bulk trimmed.
- **For a secret:** the key string (redacted to a prefix), **the URL it was found on** (Photon associates intel with its source URL since v1.3.0), and a separate, minimal request/response pair proving the key is live — never the raw crawl dump.
- **For a JS-sourced endpoint:** the line from `endpoints.txt`, the `scripts.txt` URL it came from, and a `curl` of the live endpoint showing it responds.
- **For a subdomain/takeover lead:** `subdomains.txt` plus the DNS graph image from `--dns`, and the `dig`/`curl` output showing the dangling target.
- **For a parameter bug:** Photon only supplied the candidate URL — the PoC is the single crafted request/response from dalfox/sqlmap/`curl`, with expected-vs-actual.
- **Never** attach a `--clone` mirror or exfiltrate more than the proof needs; redact tokens, and remove any test accounts/data afterward.

### Worked example — a leaked key from a JS bundle

```
# 1. Scope note: tested under example.com's HackerOne program, in-scope *.example.com
# 2. Discovery (low-volume crawl, secret scan):
photon -u https://example.com --keys -l 2 -t 2 -d 1 --timeout 10 -o example-recon
#    photon v1.3.2

# 3. keys.txt hit (redacted), with provenance from the crawl:
#    keys.txt         -> AIzaSyB************************** (len 39)
#    scripts.txt      -> https://example.com/static/app.9f2c1.js   (source of the key)

# 4. Independent, minimal validation (one request, no data pulled beyond the error/OK):
curl -s "https://maps.googleapis.com/maps/api/geocode/json?address=test&key=AIzaSyB...REDACTED" | jq .status
#    -> "OK"  (key is live and billable — confirmed, stop here)
```

The report then states: the key, where Photon found it (`app.9f2c1.js`), the single validating request, the impact (billable API abuse / quota theft), and remediation (rotate the key, move it server-side, restrict by referrer/IP). The crawl command in the report shows `-t 2 -d 1` — proof the discovery itself was low-volume.

## References

- Photon repository (s0md3v/Photon), `photon.py` current source and argparse options — https://github.com/s0md3v/Photon and https://raw.githubusercontent.com/s0md3v/Photon/master/photon.py
- Photon CHANGELOG (v1.3.2: SOCKS/rotating proxies; v1.3.0: Ninja mode removed, intel tied to source URL) — https://raw.githubusercontent.com/s0md3v/Photon/master/CHANGELOG.md
- Photon wiki, Usage (option reference; last edited 2019 — verify flags against current source) — https://github.com/s0md3v/Photon/wiki/Usage
- katana (headless/JS-aware crawler, the SPA alternative) — https://github.com/projectdiscovery/katana
- gf (grep patterns for fuzzable URLs) — https://github.com/tomnomnom/gf
- dalfox (XSS scanner, `pipe` mode) — https://github.com/hahwul/dalfox

*Research supplement added 2026-11-16, grounded in the current upstream Photon source (`photon.py`, CHANGELOG v1.3.2) and the project wiki (model: claude-opus-4-8).*
