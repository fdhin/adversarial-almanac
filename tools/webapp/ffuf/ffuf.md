---
name: "ffuf"
categories:
  - blackarch-webapp
version: "v2.3.0.r7.g8d65e03"
homepage: "https://github.com/ffuf/ffuf"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-09-28"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "ffuf/ffuf"
github-stars: 17007
github-pushed: "2026-09-26"
github-archived: false
github-issues: 246
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 33
gh-activity-checked: "2026-11-09"
---

# ffuf

> Fast, keyword-driven web fuzzer in Go — put FUZZ anywhere in a URL, header or POST body and ffuf swaps in every wordlist entry.

## What it's for

Brute-forcing anything in an HTTP request where a wordlist can be substituted: directories and files, virtual hosts, parameter names and values, POST bodies. You mark the substitution point with the `FUZZ` keyword (any word, via named wordlists) in the URL (`-u`), a header (`-H`), or POST data (`-d`), and ffuf fires one request per entry at high concurrency, then shows the responses that survive its matcher/filter rules (`-mc`/`-fc` status codes, `-fs`/`-fw`/`-fl` size/words/lines, `-mr`/`-fr` regex). It is a request generator, not a crawler or scanner: no JavaScript rendering, no vulnerability logic — recursion (`-recursion`) is available but content interpretation is yours. HTTPS targets work against self-signed certificates out of the box (current releases skip certificate verification unconditionally; the old `-k` flag is still accepted but ignored, so commands written for ffuf 1.x keep running).

## Typical use cases

### Discover directories and files

```
ffuf -u http://target.example.com/FUZZ -w /usr/share/wordlists/dirb/common.txt -c
```

Outcome: requests one path per wordlist line and prints a live table of matches (URL, status, size, words, lines) sorted as results arrive; `-c` colorizes. By default only "interesting" statuses are kept (200-299, 301, 302, 307, 401, 403, 405, 500) — everything else is already hidden, so what appears is the shortlist to visit by hand.

### Fuzz with file extensions

```
ffuf -u http://target.example.com/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words.txt -e .php,.txt,.bak -ic
```

Outcome: every wordlist entry is additionally tried with each listed extension appended (`admin`, `admin.php`, `admin.txt`, `admin.bak`), which is how backup files and forgotten scripts surface. `-ic` skips comment lines in the wordlist (SecLists files carry banner comments); `-D` instead interprets dirsearch-style wordlists that use the `%EXT%` placeholder.

### Discover recursively

```
ffuf -u http://target.example.com/FUZZ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -recursion -recursion-depth 2 -e .php
```

Outcome: when a discovered path looks like a directory, ffuf queues a new scan job inside it and keeps going down to depth 2, so one run maps nested trees instead of only the top level. The URL must end in the `FUZZ` keyword for recursion to apply; `-recursion-strategy greedy` recurses on every match instead of only redirect-indicated directories, and `-maxtime-job` bounds each queued job so one deep tree cannot eat the whole run.

### Discover virtual hosts via the Host header

```
ffuf -u http://target.example.com -H 'Host: FUZZ.target.example.com' -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fs 4242
```

Outcome: sends every request to the one IP but with a different `Host:` value each time, catching vhosts that have no DNS record. A wildcard vhost usually answers identically for unknown names — request one nonsense name first, note its response size, and pass that size to `-fs` to filter the catch-all; if the site instead returns one status for everything, `-mc 200,404` (match only those codes) is the equivalent status-based cut.

### Discover GET parameter names

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -u 'http://target.example.com/admin/?FUZZ=' -fs 4242
```

Outcome: probes the endpoint once per candidate parameter name (`?search=`, `?debug=`, ...); unknown-parameter responses share a size (the page rendering an error or unchanged content) which `-fs` removes, leaving real, accepted parameters in the results. Swap the wordlist for values instead to fuzz a known parameter's content — same mechanics, `FUZZ` moved to the value position.

### Enumerate parameter values (IDs, usernames, tokens)

```
ffuf -w <(seq 1 1000) -u 'http://target.example.com/download.php?id=FUZZ' -fw 4
```

Outcome: works through numeric IDs (or any generated list — `<(seq ...)` is bash process substitution, a file works too) and filters by word count (`-fw`) so that the uniform "not found" page drops out and valid records stay; byte size (`-fs`) does the same job when error pages have stable size. The identical shape enumerates usernames (`?user=FUZZ` with a username list) or any other value the server reflects differently when it exists.

### Fuzz POST request bodies

```
ffuf -w passwords.txt -X POST -d 'username=admin&password=FUZZ' -u http://target.example.com/login.php -fc 401
```

Outcome: fills the `FUZZ` position inside the POST body while the rest of the body stays fixed, and filters the 401s so that the success response (typically a 302 or 200 with different content) stands out; escape a literal `&` in the body as `\&`. For JSON APIs add `-H 'Content-Type: application/json'` and put `FUZZ` inside the JSON string. To fuzz an exact request captured from a browser or proxy, save the raw HTTP request to a file and use `-request file.req` (with `-FUZZ-` positions inside it and `-request-proto http`/`https`) instead of `-u`/`-H`/`-d`.

### Control what counts as a hit (matchers and filters)

```
ffuf -u http://target.example.com/FUZZ -w /usr/share/wordlists/dirb/common.txt -mc 200,204,301,302,307,401
```

Outcome: `-mc` replaces the default matcher set, keeping only the listed status codes — useful when a server returns a storm of 403/500 noise you want gone. The mirror-image flag `-fc 403,404` drops codes instead of listing keepers; `-fs 1131` and `-fw 3091` cut responses by byte size and word count (word count is steadier than size when whitespace varies), and `-mr 'Location: /admin'` keeps only responses matching a regex while `-fr` filters regex matches out. Run once with no filters and read the repeated noise values off the screen, then re-run with them filtered.

### Auto-calibrate the filters

```
ffuf -u http://target.example.com/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt -ac
```

Outcome: before the real scan, ffuf requests a few random garbage paths, measures the "soft 404" signature (status, size, words) the site returns for nonexistent pages, and filters that signature automatically — no manual size-hunting. `-ac` fails when the error page is dynamic, in which case calibrate with explicit `-fs`/`-fw` values instead; `-acc 'test123'` adds your own calibration strings, and `-ach` calibrates per host when fuzzing many vhosts against one IP.

### Tune speed: threads, rate, delay

```
ffuf -u http://target.example.com/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -rate 100
```

Outcome: `-t` sets concurrent workers (default 40 — raise it for fast targets) and `-rate` caps requests per second regardless of thread count, which keeps you under rate limits or WAF thresholds; for badly rate-limited targets drop to near-serial with `-t 1 -rate 10`. `-p 2` inserts a fixed 2-second delay per request (or a random range like `-p 0.1-2.0`), and `-maxtime 600` kills the whole process after 10 minutes so a hung scan cannot run forever.

### Combine wordlists with named keywords

```
ffuf -w usernames.txt:USER -w passwords.txt:PASS -u 'http://target.example.com/?user=USER&pass=PASS' -mode clusterbomb -fc 401
```

Outcome: each wordlist gets its own keyword after a colon, and the URL uses both — the default `-mode clusterbomb` tries every username against every password (credential stuffing), `pitchfork` walks the two lists in step, and `sniper` substitutes one list at a time into a single keyword position. A single named wordlist (`-w file.txt:FFUZ`) is the same syntax used to rename the keyword when the literal word `FUZZ` is impractical in the URL.

### Route traffic through a proxy

```
ffuf -u http://target.example.com/FUZZ -w /usr/share/wordlists/dirb/common.txt -x http://127.0.0.1:8080
```

Outcome: every request (matched or not) goes through the proxy, so Burp Suite or OWASP ZAP on `127.0.0.1:8080` sees the full scan for debugging headers, cookies and encoding; `socks5://127.0.0.1:9050` works the same way for SOCKS chaining. `-replay-proxy` sends only the matched requests through a second proxy, keeping the inspection target's traffic volume low.

### Probe CORS trust by fuzzing the Origin header

Find which origins the application treats as trusted.

```
ffuf -w subdomains.txt -u http://target.example.com -H 'Origin: http://FUZZ.target.example.com' -fs 4242
```

Outcome: every request carries a different `Origin:` value while the URL stays fixed; untrusted origins get an identical catch-all response, so filter that baseline size with `-fs` and what remains are the origins the application handles specially. Note that ffuf's regex matcher `-mr` scans the response body only — use it when the reflection appears in body content, and never combine it with `-ignore-body`, which skips fetching the body entirely (response headers cannot be matched at all).

### Fuzz characters inside a string

Use the FUZZ keyword as an infix, not just a suffix.

```
ffuf -w wordlist.txt -u 'https://target.example.com/desktop_FUZZ.zip' -mc 200,301,302,403
```

Outcome: the keyword is substituted wherever it appears in the URL, so a short list of separators or version tags (`-`, `_`, `%20`, `v2`) enumerates filename variants such as `desktop-v2.zip`, `desktop_v2.zip` and `desktop%20v2.zip` in a single run — the same mechanism as extension fuzzing, but pointing at the middle of a name. `-mc` keeps the redirect and success statuses that betray an existing file.

## Bug bounty workflow with ffuf

ffuf is a request generator, not a judge: it tells you which responses differ, never which ones matter. On a bug bounty program that distinction is the whole game — the tool's throughput is exactly what gets hunters rate-limited, WAF-banned, or reported for abuse. Every phase below is calibrated-first and rate-capped, because the single most common way to waste an ffuf run is to generate 200k requests whose results you cannot tell apart from the site's soft-404 page. ffuf and [[dirsearch]] solve the same core problem (path/content discovery by wordlist); the cross-links below mark where their coverage overlaps and where ffuf's keyword model reaches surfaces dirsearch does not (vhosts, headers, POST bodies, multi-keyword combinatorics).

**Ground rules for every phase.** Content and parameter fuzzing is active and loud — there is no passive mode. Confirm the exact host is in the program scope *before* the first request (an in-scope apex rarely authorizes `*.`-subdomain brute forcing — enumerate hosts with [[subfinder]]/[[amass]] and fuzz only what the program lists). Keep concurrency and rate low on production: the default `-t 40` with no `-rate` is a load test, not reconnaissance. Cap wall-clock with `-maxtime`/`-maxtime-job` so a runaway recursion cannot run overnight. And record a baseline response for every surface before you fuzz it — your matchers and filters are only as good as the "normal" you measured.

### Phase 0 — Scope lock and baseline calibration

Goal: establish what a *miss* looks like on this target, so the matcher/filter stack separates signal from the catch-all.

```
# Baseline: a path that cannot exist. Record status, size, words, lines.
ffuf -u http://target.example.com/FUZZ -w <(printf 'zzz-not-a-real-path-19518\n') -c

# Then let ffuf learn the soft-404 signature itself
ffuf -u http://target.example.com/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt \
     -ac -t 10 -rate 40 -maxtime 120
```

`-ac` fires a handful of random probes, measures the status/size/words the site returns for nonexistent pages, and installs that as an automatic filter. This is the philosophy that makes the whole tool usable: **a matcher without calibration is noise.** If the site's 404 is a static page, `-ac` nails it. If the error page is *dynamic* (rotating CSRF token, timestamp, request-id echoed in the body), `-ac` fails — its single size no longer matches — and you fall back to word-count filtering (`-fw`, steadier than bytes when only small fields vary) or a regex filter (`-fr 'Not Found'`) keyed on the stable error string. Note the baseline numbers on paper; you will reuse them in `-fs`/`-fw` across later phases.

### Phase 1 — Broad content discovery (shortlist, then depth)

Goal: a first map of reachable paths, cheaply, before committing to a 220k-word list.

Work the wordlists in increasing cost: `common.txt` (~4.7k, dirb/SecLists) first for the quick wins, then `raft-medium-directories.txt` / `raft-medium-files.txt` (the raft lists are frequency-ordered from real-world crawls, so the high-value hits land early and you can stop a run that is clearly dry), and only escalate to `directory-list-2.3-medium.txt` (~220k) when the program is large and the first passes were thin.

```
ffuf -u http://target.example.com/FUZZ \
     -w /usr/share/seclists/Discovery/Web-Content/common.txt \
     -ac -ic -t 20 -rate 60 -o ffuf-common.json
```

`-ic` drops SecLists banner comments; `-o` captures the run as evidence from the start. Read the result table, then re-run the raft lists against any directory that looked alive. This is the same job [[dirsearch]] does with its bundled `db/dicts.txt` and `--wordlist-categories`; reach for whichever you have calibrated, and use ffuf when you need a wordlist or keyword position dirsearch cannot express.

### Phase 2 — Recursion and extension expansion (do the traffic math first)

Goal: map nested trees without generating a request flood you cannot defend in a report.

Recursion is where ffuf quietly turns a reconnaissance scan into a denial-of-service. Each discovered directory queues a *new job that runs the entire wordlist again*, and `-e` multiplies every wordlist entry by (1 + number of extensions). The request count is multiplicative, not additive:

> **requests ≈ W × (1 + E) × (1 + D)** per recursion level
> where **W** = wordlist entries, **E** = extensions in `-e`, **D** = directories discovered at that level.

A `directory-list-2.3-medium` run (W ≈ 220,000) with `-e .php,.html,.bak` (E = 3) is already 880,000 requests for the top level alone. Discover 20 directories and recurse one level and you are near **18 million requests against one host** — that is the shape of an abuse report, not a finding. Keep recursion bounded and the wordlist small when it is on:

```
ffuf -u http://target.example.com/FUZZ \
     -w /usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt \
     -recursion -recursion-depth 2 -e .php,.bak \
     -ac -t 10 -rate 50 -maxtime-job 300 -maxtime 1800 \
     -o ffuf-recurse.json
```

The URL **must** end in `FUZZ` for recursion to apply. `-recursion-depth 2` caps how deep queued jobs go; `-maxtime-job 300` bounds each directory's job so one deep tree cannot eat the run; `-maxtime 1800` is the hard stop for the whole process. `-recursion-strategy greedy` recurses on *every* match rather than only redirect-indicated directories — it finds more but multiplies D hard, so reserve it for small, explicitly-permitted scopes. Prefer a shallow recursion with a small list over a deep one with a big list; you can always re-run a specific subtree by hand.

### Phase 3 — Fuzzing beyond paths (the surfaces dirsearch cannot reach)

Goal: use the `FUZZ` keyword where it is not a path — this is ffuf's real edge over a pure directory brute-forcer.

**Virtual host discovery.** Fuzz the `Host:` header against a single IP to find apps with no DNS record — staging, admin, internal tools co-hosted on the same server:

```
ffuf -u http://target.example.com -H 'Host: FUZZ.target.example.com' \
     -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
     -t 10 -rate 50 -ac -ach
```

A wildcard vhost answers identically for every unknown name, so calibrate: `-ac` learns the catch-all size, and `-ach` (per-host autocalibration) is the one to add when you fuzz many Host values against one IP. If the catch-all size is stable, `-fs <size>` is the explicit equivalent. New vhosts are frequently out of the REST-discovery picture and under-tested — treat each as a fresh target for Phases 1–2.

**Parameter-name discovery.** Hidden GET/POST parameters are the feeders for IDOR, LFI, SSRF, and debug toggles that the documented API never mentions:

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
     -u 'http://target.example.com/profile?FUZZ=ffufhashcheck' \
     -ac -fw <baseline-words> -t 15 -rate 60
```

Unknown parameters render the unchanged page (uniform word count → `-fw` removes it); an accepted parameter changes the response (reflection, a new field, a different length, an error). Move `FUZZ` into the value position to enumerate IDs/usernames/tokens, and feed a generated list via process substitution (`-w <(seq 1 1000)`) or `-input-cmd` for programmatic inputs.

**POST body / JSON fuzzing with a data template.** Keep the body fixed and fuzz one field:

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
     -X POST -H 'Content-Type: application/json' \
     -d '{"FUZZ":"ffufhashcheck"}' \
     -u http://target.example.com/api/v1/search \
     -ac -t 10 -rate 40
```

Escape a literal `&` in a form body as `\&`. For an exact request captured in Burp/ZAP, drop `-u`/`-H`/`-d` and use `-request captured.req` with `FUZZ` placed inside the file and `-request-proto http|https` — this preserves cookies, ordering, and odd headers the manual flags would mangle.

**Multi-keyword combinatorics.** Named wordlists (`-w list:KEY`) plus `-mode` express tests no single-list tool can: `clusterbomb` (default) tries every combination, `pitchfork` walks lists in lockstep (paired user/pass lines), `sniper` substitutes one position at a time. Credential-style combinatorics are DoS-adjacent — only on explicit permission, and rate-capped hard.

### Phase 4 — Targeted fuzzing (backups, source, 403 bypass)

Goal: convert the map into high-value disclosures.

Append backup/extension permutations to known filenames (`-e .bak,.old,.zip,.tar.gz,~,.swp`), fuzz infix separators to catch `app-v2.zip`/`app_backup.zip` variants (see the infix recipe above), and probe forbidden paths for normalization bypasses by fuzzing the path around a known 403 (trailing `..;/`, `%2e`, `/./`, case changes). Route everything through a proxy with `-x http://127.0.0.1:8080` so Burp/ZAP holds the full request/response history you will cite, or `-replay-proxy` to send only matched hits to the inspection proxy and keep its volume low.

### Phase 5 — Validate and capture evidence

Goal: a clean, reproducible record for the report (see Reporting evidence below). Re-run the single confirming request through a proxy, export machine-readable output (`-o out.json -of json`), and save the matched responses to disk (`-od ./responses/`) so the proof is attached, not screenshotted.

### ffuf or [[dirsearch]] — which to reach for

They overlap on the core job (wordlist-driven path discovery) and diverge at the edges; a hunter usually keeps both.

| Need | Reach for | Why |
|---|---|---|
| Path/file discovery with sensible defaults, categorized wordlists | [[dirsearch]] | Bundled dictionaries, `--wordlist-categories`, `%EXT%` templating, report formats out of the box |
| `FUZZ` anywhere non-path: Host, Origin, arbitrary header, POST/JSON body | **ffuf** | Keyword model places the payload wherever you write it |
| Multiple simultaneous wordlists (clusterbomb/pitchfork/sniper) | **ffuf** | `-w list:KEY` + `-mode`; dirsearch is single-list |
| Fuzzing a raw captured request with cookies/ordering intact | **ffuf** | `-request file.req` preserves the capture verbatim |
| Authenticated scan that refreshes a CSRF/session token mid-run | **ffuf** | preflight requests (v2.3.0+) |
| Purpose-built content-discovery wordlist categories and `--deep-recursive` | [[dirsearch]] | Discovery-specific ergonomics |

When both fit, run whichever you have calibrated for the target and corroborate surprising hits with the other — a path one tool filters as a soft-404 the other may surface, because their calibration heuristics differ.

## Where the money is with ffuf

ffuf rarely *is* the vulnerability; it is the discovery step that hands you the vulnerable surface. The table ranks by payout of what the discovery leads to, and flags dupe risk — content discovery is the most-automated step in bug bounty, so "I found `/admin`" alone is a closed dupe on any mature program.

| Priority | Finding class | ffuf feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Exposed source / backups (`.git`, `.zip`, `.bak`, `.sql`, `.env`) | path discovery + `-e` extension/infix fuzzing | High–Critical | Medium — scanners find these fast; be thorough and first |
| 2 | Hidden virtual hosts (staging, admin, internal apps) | `Host: FUZZ` vhost fuzzing with `-ac -ach` | High (often unauthenticated internal app) | Low — vhost fuzzing is underused |
| 3 | Hidden parameters → IDOR / LFI / SSRF / debug | parameter-name fuzzing (`?FUZZ=` / `{"FUZZ":...}`) | depends on the sink it feeds | Low–Medium — the param is novel even if the bug class isn't |
| 4 | Forgotten admin / internal / debug endpoints | path discovery; recursion into discovered trees | High (BFLA if unauthenticated) | Medium–High for obvious names |
| 5 | 403 / auth bypass via path normalization | fuzzing the path around a known-forbidden route | Medium–High | Low–Medium |
| 6 | Enumerable objects (IDs, usernames, invite tokens) | value fuzzing (`FUZZ` in value, `-w <(seq ...)`) | Medium–High (enumeration / IDOR) | Medium |
| 7 | CORS trust / origin reflection | `Origin: FUZZ` header fuzzing | Medium | Low |
| 8 | Login / weak-credential surfaces | POST body fuzzing (permission required) | High if it lands | High (and abuse-prone) |

Rows 1–3 are where ffuf's keyword model pays off over a generic directory brute-forcer and where the dupe risk is lowest. Row 8 is last on purpose: credential brute forcing is the fastest way to get banned and the most likely to be out of scope — never run it without explicit, written permission and a hard rate cap.

## Command recipes

Every recipe below is tuned for a production bug-bounty target: low threads, an explicit rate cap, calibration on, evidence captured. Raise the numbers only on a target the program says you may load-test.

**Calibrated directory discovery, evidence saved.**
```
ffuf -u https://target.example.com/FUZZ \
     -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt \
     -ac -ic -c -t 15 -rate 60 -maxtime 1200 \
     -o ffuf-dirs.json -of json
# -> live table of surviving paths; JSON record for the report.
#    -ac removes the soft-404 signature; -rate 60 stays polite; -maxtime caps the run.
```

**Extensions for backups and source, dynamic-404 fallback.**
```
ffuf -u https://target.example.com/FUZZ \
     -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt \
     -e .bak,.old,.zip,.sql,.txt,.swp -ic \
     -fw 42 -t 10 -rate 50
# -> admin.bak, config.old, db.sql surface alongside their base names.
#    Use -fw (word count) when -ac fails on a dynamic error page; set the value
#    from the Phase 0 baseline. -fs is the byte-size equivalent for stable pages.
```

**Bounded recursion (mind the traffic math).**
```
ffuf -u https://target.example.com/FUZZ \
     -w /usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt \
     -recursion -recursion-depth 2 -e .php \
     -ac -t 10 -rate 50 -maxtime-job 300 -maxtime 1800
# -> queues a sub-job per discovered directory, down to depth 2.
#    Small wordlist on purpose: W x (1+E) x (1+D) grows fast. URL must end in FUZZ.
```

**Virtual host sweep against one IP.**
```
ffuf -u https://target.example.com \
     -H 'Host: FUZZ.target.example.com' \
     -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
     -ac -ach -t 10 -rate 50
# -> vhosts with no DNS record. -ach calibrates per host to kill the wildcard catch-all.
#    If the catch-all size is stable, -fs <size> is the explicit filter.
```

**Hidden parameter discovery.**
```
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
     -u 'https://target.example.com/profile?FUZZ=ffufprobe' \
     -ac -t 15 -rate 60
# -> accepted parameters change the response; the unchanged page is filtered by -ac.
#    Pivot each hit into IDOR/LFI/SSRF testing by hand.
```

**JSON API field fuzzing from a captured request.**
```
ffuf -request /tmp/search.req -request-proto https \
     -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
     -mode sniper -ac -t 10 -rate 40
# -> fuzzes each FUZZ position in the raw request; preserves cookies/headers/ordering
#    exactly as captured, which -u/-H/-d would not.
```

**Match-set explicit control (replace the defaults).**
```
ffuf -u https://target.example.com/FUZZ -w common.txt \
     -mc 200,204,301,302,401,403 -fs 0 -mmode or
# -mc replaces the default set (200-299,301,302,307,401,403,405,500); -fs 0 drops
# empty bodies; -mmode/-fmode set whether multiple matchers combine with and/or.
```

**Full proxy capture for evidence.**
```
ffuf -u https://target.example.com/FUZZ -w common.txt \
     -x http://127.0.0.1:8080 -replay-proxy http://127.0.0.1:8081 \
     -ac -t 10 -rate 40 -od ./ffuf-responses/
# -x routes every request through Burp/ZAP; -replay-proxy sends only matches to a
# second proxy; -od writes matched response bodies to disk as attachable proof.
```

### The matcher/filter matrix

Matchers (`-m*`) decide what is *kept*; filters (`-f*`) decide what is *dropped*. Filters win when both apply. Every dimension has a matcher and a filter form, so choose whichever expresses the smaller, more stable set. The default matcher is `-mc 200-299,301,302,307,401,403,405,500`; **setting any matcher replaces that default**, so if you add `-mr` you must restate `-mc` or you lose the status pre-filter.

| Dimension | Match | Filter | When to reach for it |
|---|---|---|---|
| Status code | `-mc 200,403` / `-mc all` | `-fc 404,500` | First cut; `all` disables the status pre-filter entirely |
| Response size (bytes) | `-ms 1234` | `-fs 0,4242` | Stable pages; exact catch-all size; comma lists and ranges allowed |
| Word count | `-mw 92` | `-fw 42` | Steadier than size when only small fields (tokens, timestamps) vary |
| Line count | `-ml 10` | `-fl 1` | Template pages differing by a few rows |
| Regex (body only) | `-mr 'admin'` | `-fr 'Not Found'` | Dynamic 404s; reflection in the body — never with `-ignore-body` |
| Time to first byte | `-mt >100` | `-ft <20` | Time-based signals (blind injection, slow auth paths) |

Combine several with the set operator: `-mmode and` requires *all* matchers to pass (default is `or`), and `-fmode and` likewise for filters — use `and` to express "200 **and** body contains `token` **and** larger than the error page" precisely. The regex matcher scans the **response body only**; response headers cannot be matched, and pairing `-mr`/`-fr` with `-ignore-body` is a silent no-op because the body is never fetched.

### Authenticated fuzzing (preflight — v2.3.0+)

Most high-value surfaces are behind login, and a stale cookie expires mid-scan. Recent ffuf (v2.3.0) can run a **preflight** request that fetches a fresh token before fuzzing and feeds it into the main request, so an authenticated scan does not die halfway:

```
# preflight.req logs in / fetches the page carrying the CSRF token;
# -preflight-var captures it by regex into a keyword the main request uses.
ffuf -request /tmp/update.req -request-proto https \
     -preflight /tmp/login.req \
     -preflight-var 'CSRF:name="csrf_token" value="([^"]+)"' \
     -preflight-mode per-thread \
     -w params.txt -ac -t 5 -rate 30
# -> each thread re-runs the preflight to keep CSRF/session fresh;
#    -preflight-mode per-request refreshes on every request (slower, strictest).
```

Newer builds add `-preflight-var-auto` (capture a named value from a form field, meta tag, cookie, or response header without writing the regex) and gate credential leakage with `-preflight-anyhost` — by default a captured value cannot redirect a later request to a different scheme/host/port, so a scanned target cannot steer your operator credentials elsewhere. For simpler cases, static auth still works inline: `-H 'Authorization: Bearer <token>'` or `-b 'session=<cookie>'`.

### Encoders, config profiles, and the interactive console

**On-the-fly encoding** (`-enc`, v2.1.0+, via `ffuf/pencode`) transforms a keyword before it is sent — `-enc 'FUZZ:urlencode'` or chained `-enc 'FUZZ:b64encode urlencode'` — useful for WAF evasion checks and for fuzzing values that must arrive URL- or base64-encoded. Use it only where the program permits evasion testing.

**Reusable rate profiles.** Put your safe defaults (`-t`, `-rate`, `-maxtime`, `-ac`) in `$XDG_CONFIG_HOME/ffuf/ffufrc` or a named file loaded with `-config profile.conf`, so every engagement starts polite by default and you cannot forget the cap. Command-line flags override the file; `-H` values are appended to it.

**The interactive console** (press ENTER during a run) tunes a live scan without restarting: `fc`/`fs`/`fw`/`ft` set filters, `afc`/`afs`/… *add* to them, `rate` changes the cap on the fly, `queueshow`/`queuedel`/`queueskip` manage recursion jobs, and `savejson <file>` snapshots results so far. This is how you throttle *down* the instant a target starts returning 429s instead of killing the run and losing the queue. `-noninteractive` disables it for scripted/CI use.

## Pitfalls, noise and scope discipline

**The default throughput is a load test.** `-t 40` with no `-rate` against a wordlist of hundreds of thousands of entries is how hunters trip WAFs, exhaust rate limits for real users, and earn abuse reports. Set `-t` low and `-rate` explicitly on anything production; ffuf obeys `-rate` regardless of thread count, so it is the cap that actually protects you. For a brittle target, `-t 1 -rate 10` with `-p 0.1-1.0` (jittered delay) is near-serial and far less likely to be noticed.

**Recursion is the multiplier that gets you banned.** Re-read the Phase 2 traffic math before enabling `-recursion` on a large wordlist. An unbounded recursive run with `directory-list-2.3-medium` and three extensions against a single-host program is tens of millions of requests — indistinguishable from a deliberate DoS. Always pair recursion with `-recursion-depth`, `-maxtime-job`, and a small wordlist, and prefer `-recursion-strategy default` (redirect-indicated) over `greedy` unless the scope is tiny and explicitly permits it.

**Uncalibrated filters produce garbage, both ways.** Skip `-ac` (or a manual `-fs`/`-fw`) and the result table fills with the soft-404 page repeated thousands of times — real hits drown. Over-filter (an `-fs` value that happens to match a legitimate page's size) and you silently drop the finding. When `-ac` fails on a dynamic error page, do not just remove it: switch to word-count (`-fw`) or a regex filter (`-fr 'Not Found'`) on the stable part of the error. Verify the filter by confirming a known-good path still appears.

**Scope creep through wordlists and keywords.** ffuf brute-forces whatever you point `FUZZ` at — so a `Host:` or `Origin:` wordlist containing out-of-scope names, or a vhost sweep against an IP that also serves assets you were not authorized for, is a real violation even though you only changed a header. Vet the wordlist's contents against program scope. `*.`-subdomain discovery is a *separate* activity ([[subfinder]]/[[amass]]) and is only in scope when the program explicitly allows subdomain brute forcing.

**False positives from catch-all behavior.** Wildcard DNS, wildcard vhosts, SPA routers that 200 everything, and "friendly" 404s that return 200 with a body all defeat status-only matching. This is why the workflow is calibration-first: measure the catch-all, filter it, and confirm a positive control survives before trusting any result.

**Don't exfiltrate during discovery.** `-od` saves full response bodies — useful for evidence, but it means you are storing the target's data. Save only what the proof needs, redact secrets before they go in a report, and delete the capture afterward. The same discipline applies to any value fuzzing that returns real user records.

## Reporting evidence

ffuf's value in a report is reproducibility: a triager should be able to re-run one request and see what you saw. Capture, per finding:

- **The exact ffuf invocation** — full command line including wordlist path and version (`ffuf -V`), so the discovery is reproducible. State the wordlist by name and source.
- **Machine-readable run output** — `-o finding.json -of json` (or `-json` for newline-delimited records). The JSON carries status, size, words, lines, the resolved URL, redirect location, and the `FFUFHASH` for each hit, which lets you prove exactly which payload produced the result (`-search <FFUFHASH>` recovers the input from history).
- **The single confirming request/response pair** — not the whole scan. Re-issue the one hit through a proxy (`-x`) or with `curl` and paste the full request and response headers plus the relevant body. For a matched-only capture, `-od ./responses/` writes the response bodies ffuf already fetched.
- **The baseline for contrast** — show the soft-404/catch-all response alongside the finding so the difference that made it a hit is self-evident (status, size, or the field that changed). This is what turns "ffuf highlighted it" into "here is why it is real."
- **Impact, not inventory.** A list of discovered paths is recon, usually a dupe. Promote the one that matters: the `.git/config` that dumps source, the staging vhost with no auth, the `?debug=` parameter that returns a stack trace. Lead the report with that, and attach the discovery evidence as support.
- **Scraper-extracted context.** ffuf's data scraper (`-scrapers`, `-scraperfile`, v2.0.0+) pulls defined values out of matched responses — page titles, a version string, a reflected marker — into the output record, so the JSON evidence already shows *why* each hit is interesting without a second fetch. Define a small scraper for the target's sensitive shapes and the proof writes itself.

Frame volume honestly in the report: note the `-t`/`-rate` you used and the total request count, which signals to the triager that the testing was controlled and in line with the program's rules of engagement.

## References

- ffuf — official repository and README — https://github.com/ffuf/ffuf
- ffuf README (raw, `master`) — flag reference and defaults — https://raw.githubusercontent.com/ffuf/ffuf/master/README.md
- ffuf CHANGELOG (raw, `master`) — version history and feature additions — https://raw.githubusercontent.com/ffuf/ffuf/master/CHANGELOG.md
- ffuf wiki — usage and recipes — https://github.com/ffuf/ffuf/wiki
- SecLists — Discovery/Web-Content and Discovery/DNS wordlists — https://github.com/danielmiessler/SecLists
- Companion content-discovery tool in this vault — [[dirsearch]]; host enumeration before scoped fuzzing — [[subfinder]], [[amass]]

*Research supplement added 2026-11-16, grounded in the ffuf README/CHANGELOG on `master` (v2.3.0, help block labelled v2.1.0) and the ffuf wiki (model: claude-opus-4-8).*
