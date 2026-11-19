---
name: "katana-pd"
categories:
  - blackarch-webapp
version: "v1.6.1.r285.g6f226653"
homepage: "https://github.com/projectdiscovery/katana"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/katana"
github-stars: 17810
github-pushed: "2026-11-06"
github-archived: false
github-issues: 33
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 551
gh-activity-checked: "2026-11-09"
---

# katana-pd

> ProjectDiscovery's fast web crawler — standard and headless-browser crawling that enumerates endpoints, parses JavaScript for hidden routes and extracts forms.

## What it's for

katana (binary name `katana`; the vault entry is suffixed to disambiguate from other same-named tools) is a crawling and spidering framework for attack-surface enumeration. In standard mode it crawls with the plain Go HTTP stack — fast, no browser; in headless mode it drives a real (Chromium-based) browser, so DOM-rendered links and JavaScript-triggered requests are captured too. It parses JavaScript files for endpoints, crawls known files (robots.txt, sitemap.xml), extracts form/input elements, enforces flexible scoping and can store full request/response traffic. Reach for it as the first stage of web recon: the endpoint list it emits is what you feed to fuzzers and proxies. It is a crawler, not a vulnerability scanner — it discovers, you attack.

## Typical use cases

### Crawl a single site

The basic enumeration run.

```
katana -u https://target.example.com -d 4
```

Outcome: crawls to depth 4 (default 3) and prints discovered endpoints to stdout, one URL per line; `-rl` caps request rate (default 150/s) and `-c` sets concurrent fetchers (default 10).

### Parse JavaScript and known files for endpoints

Squeeze hidden routes out of client-side code.

```
katana -u https://target.example.com -jc -kf all
```

Outcome: `-jc` additionally parses every crawled JavaScript file for endpoint strings and queues them as crawl targets; `-kf all` also fetches and parses `robots.txt` and `sitemap.xml` (a minimum depth of 3 is required for full known-file coverage) — the combination surfaces endpoints that exist only inside bundle files.

### Crawl with a real browser

Headless mode for JavaScript-heavy applications.

```
katana -u https://target.example.com -headless -no-sandbox -system-chrome
```

Outcome: pages are rendered in a headless browser (use `-system-chrome`/`-scp` to pick an installed Chrome), so dynamically generated links and async calls appear in the output; `-no-sandbox` for root contexts, `-sb` to watch the browser, `-al user:Password1` to auto-login before crawling, and `katana -h headless` lists the browser-specific flags.

### Keep the crawl on scope

Constrain or widen what counts as in-scope.

```
katana -u https://target.example.com -fs "(target.example.com|example.com)" -do -mr "admin|api"
```

Outcome: `-fs` accepts a scope field (`dn`, `rdn`, `fqdn`) or a custom regex of allowed hosts; `-do` also displays out-of-scope endpoints found on scoped pages, and `-mr` filters output to URLs matching the given regex(es) — the standard shape for bug-bounty scope rules.

### Crawl from a list and store results

Batch input with file output.

```
katana -list urls.txt -o endpoints.txt -j -fx -sr
```

Outcome: crawls every URL in the file, writes findings to `endpoints.txt` as JSONL (`-j`), includes extracted form/input/textarea/select elements in that output (`-fx`), and `-sr` saves the full HTTP request/response of each finding to disk for later inspection; `-srd` chooses the directory.

## Bug bounty workflow with katana

katana is a discovery tool, not an attack tool: its job is to hand every other stage a clean, in-scope, deduplicated list of URLs and parameters. Treat it as the crawl stage of a pipeline — `subfinder` → `httpx` → **katana** → (`gf` / `dalfox` / `nuclei`) — and the single rule that keeps you inside program rules is *scope is enforced at the crawler, not downstream*. If katana emits an out-of-scope URL, something later will hit it.

Everything below assumes an authorized program, written permission, the program's rate rules, and the low-volume defaults in Command recipes. katana's shipped defaults (150 req/s, concurrency 10, headless browser automation, experimental form auto-fill) are tuned for speed, not for staying quiet and in-scope — you lower them deliberately.

### Phase 0 — Scope compilation (before the first request)

Turn the program's in-scope list into a regex you can hand to `-field-scope`, so the crawler *physically cannot* queue an out-of-scope host. `-fs` accepts the named scopes `dn`/`rdn`/`fqdn` **or a custom regex of allowed hosts** — the regex form is what matches a real bounty scope.

```bash
# Program scope: *.example.com and app.example-cdn.net, nothing else.
# Build the allow regex once; reuse it on every katana invocation.
SCOPE='(^|\.)(example\.com|example-cdn\.net)$'
```

Anchor the regex. An un-anchored `example\.com` also matches `notexample.com`, `example.com.evil.net`, and `example-com.attacker.io` — all out of scope, and crawling any of them is a scope violation you authored yourself. The `(^|\.)` prefix (host is exactly the domain, or a subdomain of it) and the trailing `$` (nothing after the TLD) close both holes. Dry-run the regex against a handful of allowed and disallowed hostnames before you point it at a target. Keep a separate `SCOPE` per program; never reuse one across engagements.

Record the program's rate ceiling and whether active crawling, form submission, and authenticated crawling are permitted. Three katana behaviors are scope/impact landmines to decide on *now*, not mid-run: `-automatic-form-fill` (submits forms), `-kb-validate-secrets` (sends live API calls to test discovered secrets — real traffic to third parties, frequently out of scope), and `-no-scope` (disables scoping entirely — never in a bounty).

### Phase 1 — Seed from passive recon, don't crawl blind

katana crawls what you feed it. Feed it a probed, live host list, not raw subdomains — crawling dead or parked hosts wastes your rate budget and pollutes output.

```bash
# subfinder -> httpx (probe live) -> katana (crawl), scope-locked throughout
subfinder -d example.com -silent \
  | httpx -silent -mc 200,204,301,302,307,401,403 \
  | katana -silent -fs "$SCOPE" -rl 10 -c 5 -d 3 -jsonl -o crawl.jsonl
```

Outcome: `httpx` ([[httpx]]) collapses the subdomain list to hosts that actually answer; katana ([[subfinder]] feeds the chain) crawls each at depth 3, 10 req/s, 5 concurrent fetchers, writing one JSON record per discovered endpoint to `crawl.jsonl`. The `-fs "$SCOPE"` is the load-bearing flag: no out-of-scope host enters the queue even if a page links to one.

For a saved host list, `-list`/`-u` accepts a file (or stdin, or a comma-separated string). When crawling many hosts, `-parallelism`/`-p` controls how many *targets* run at once while `-concurrency`/`-c` controls URLs *within* each target — keep both low so the aggregate request rate across the whole list stays inside the program's ceiling (a `-p 5 -c 5` run is up to 25 in-flight requests, not 5):

```bash
katana -list live-hosts.txt -fs "$SCOPE" -p 3 -c 5 -rl 10 -d 3 \
  -ct 15m -jsonl -o batch.jsonl
```

### Phase 2 — Standard crawl first (fast, no browser)

Standard mode uses the plain Go HTTP stack — no Chrome, low footprint, high speed. Run it first on every target and only escalate to headless where it demonstrably misses content (Phase 4). For most server-rendered apps, standard mode plus JS parsing (Phase 3) finds everything headless would.

```bash
katana -u https://app.example.com -fs "$SCOPE" -d 4 -rl 10 -c 5 \
  -ct 10m -jsonl -o app-standard.jsonl
```

`-ct 10m` (`-crawl-duration`) is the safety valve: the crawl stops after ten minutes regardless of depth, so a link-farm or calendar widget cannot run you into the program's daily rate budget.

### Phase 3 — Squeeze endpoints out of JavaScript and known files

The endpoints that aren't in the HTML are the valuable ones: API routes referenced only inside bundled JS, paths listed in `robots.txt`, stale routes in `sitemap.xml`.

```bash
katana -u https://app.example.com -fs "$SCOPE" -d 4 -rl 10 -c 5 \
  -jc -kf all -jsonl -o app-deep.jsonl
```

`-jc` (`-js-crawl`) parses every crawled `.js` file for endpoint strings and queues them as new crawl targets; `-kf all` (`-known-files`) fetches and parses `robots.txt` and `sitemap.xml` — **`-kf` needs depth ≥ 3 for full coverage**, so pair it with `-d 3` or higher. For deeper bundle parsing, add `-jsl` (`-jsluice`), katana's JS secret/endpoint extractor — note it is **memory intensive** and slower, so reserve it for a focused second pass on the few JS-heavy hosts that matter.

### Phase 4 — Headless only when standard mode comes up short

Headless mode drives a real Chromium browser, so DOM-rendered links and JavaScript-triggered XHR appear in the output — the thing standard mode cannot see on a client-rendered SPA (React/Vue/Angular with an empty-ish initial HTML body). The cost is real: Chrome per worker, far more CPU/RAM, slower crawls, and `-store-response` is **not supported in headless mode**. Escalate deliberately.

| Dimension | Standard mode (default) | Headless mode (`-headless`) |
|---|---|---|
| Engine | Go HTTP client | Chromium browser |
| Sees DOM-rendered / JS-injected links | No | Yes |
| Captures XHR/fetch API calls | No (only via `-jc` parsing of the JS source) | Yes, with `-xhr-extraction` |
| Speed / footprint | Fast, light | Slow, heavy (Chrome per worker) |
| `-store-response` (`-sr`) | Supported | **Not supported** |
| When to reach for it | Every target, first | SPA where standard returns only the shell |

The honest default: run standard mode with `-jc` on everything; escalate to headless only for the hosts where standard mode returns almost nothing but the HTML skeleton. Headless is not "more thorough," it is "renders JavaScript" — for server-rendered apps it adds cost and finds nothing new.

```bash
# Decision signal: standard crawl of an SPA returns almost nothing but the shell.
katana -u https://spa.example.com -fs "$SCOPE" -headless -no-sandbox \
  -system-chrome -xhr-extraction -d 3 -rl 10 -c 5 -ct 10m -jsonl -o spa.jsonl
```

`-headless` (`-hl`) enables the browser engine; `-system-chrome` (`-sc`) uses an installed Chrome instead of katana's download (use `-system-chrome-path`/`-scp` to point at a specific binary); `-no-sandbox` (`-nos`) is required when running as root in a container; `-xhr-extraction` (`-xhr`) captures the async API calls the app fires. Add `-show-browser` (`-sb`) once to watch the crawl and confirm it is logging in / navigating as expected.

### Phase 5 — Authenticated crawl (the surface behind login)

Most bounty value sits behind authentication. Give katana a valid session and it maps the authenticated surface — but authenticated crawling is where you risk firing state-changing requests, so pair it with the form discipline in Pitfalls.

```bash
# Standard mode: inject a captured session via -headers (-H); value or a file path.
katana -u https://app.example.com -fs "$SCOPE" -d 3 -rl 10 -c 5 \
  -H 'Cookie: session=<captured-session>' -H 'Authorization: Bearer <token>' \
  -jsonl -o app-authed.jsonl
```

There is no dedicated `-cookie` flag — cookies ride through `-H`. For headless, `-auto-login`/`-al` (`user:password`, headless only) can log in before crawling, and `-chrome-ws-url`/`-cwu` attaches katana to a Chrome you already logged into manually (the most reliable path for MFA/SSO flows). Crawl logged-out and logged-in separately and diff the two endpoint sets — routes that appear only in the authenticated crawl are your freshest attack surface.

One authenticated-crawl trap worth pre-empting: the crawler will happily follow a `GET /logout` link and kill its own session halfway through, so the rest of the crawl silently runs unauthenticated and under-reports. Fence off the destructive and session-ending paths before you start:

```bash
katana -u https://app.example.com -fs "$SCOPE" -d 3 -rl 10 -c 5 \
  -H 'Cookie: session=<captured-session>' \
  -crawl-out-scope 'logout|signout|delete|revoke|deactivate' \
  -jsonl -o app-authed.jsonl
# -> -crawl-out-scope (-cos) keeps matching URLs out of the crawl queue: the session
#    survives and no state-changing link is auto-followed.
```

### Phase 6 — Feed the pipeline and track over time

The crawl output is an input, not a finding. Fan it out:

```bash
# Extract just the URLs from the JSONL for downstream tools
jq -r '.request.endpoint' crawl.jsonl | sort -u > urls.txt

# Parameters worth attacking -> gf patterns -> targeted tools
gf xss      < urls.txt | dalfox pipe              # reflected XSS candidates -> dalfox
gf redirect < urls.txt                            # open-redirect candidates
nuclei -l urls.txt -rl 10 -c 5                    # template scan on discovered URLs
```

`dalfox` ([[dalfox]]) tests the parameterized URLs for XSS; `nuclei` ([[nuclei]]) runs templates against the discovered endpoint list. Re-run katana on a cadence and diff the URL sets (`comm -13 old.txt new.txt`): newly shipped endpoints are the least-reviewed code on the target and the best dupe-free leads.

A whole-program one-liner, scope-locked end to end:

```bash
subfinder -d example.com -silent \
  | httpx -silent \
  | katana -silent -fs "$SCOPE" -jc -kf all -rl 10 -c 5 -d 3 -ct 15m \
  | uro \
  | tee all-urls.txt \
  | nuclei -rl 10 -c 5 -o nuclei-$(date +%F).txt
```

`uro` deduplicates near-identical URLs (pagination, tracking params) before they reach `nuclei`, so the scope and rate discipline survives the hand-off. `tee` keeps a dated snapshot for the next diff.

Common follow-on consumers of the URL list, by goal:

- **`gf` + `dalfox`** — pattern-match params, then test for XSS.
- **`nuclei`** ([[nuclei]]) — template scan (tech detection, exposures, CVEs) on the endpoint list.
- **`ffuf`** — fuzz the discovered directories/params, seeded from `-store-field` output.
- **`qsreplace` / `uro`** — rewrite or collapse query strings before fuzzing.

Pick the ones the finding class calls for; the crawl output is the shared input to all of them.

### Phase 7 — Knowledge base: secrets and endpoints

katana can build a structured knowledge base from the crawl instead of just a URL stream. `-kb`/`-knowledge-base` turns it on; `-kb-endpoints` catalogs discovered endpoints and `-kb-secrets` extracts secrets (API keys, tokens) spotted in JavaScript and responses. With `-kb`, each JSONL record gains a `knowledgebase` field.

```bash
katana -u https://app.example.com -fs "$SCOPE" -d 4 -jc -jsl \
  -kb -kb-secrets -rl 10 -c 5 -jsonl -o kb.jsonl
# -> endpoints + any JS-embedded secrets land in the knowledgebase field, no live traffic sent.
```

Leave `-kb-validate-secrets` **off**: it sends live API calls to prove a key works — real, often out-of-scope and billable traffic to third parties. Validate any promising secret by hand, only against in-scope services, and report the leak itself (where the key appeared, what it is) rather than its blast radius unless the program authorizes active testing.

## Where the money is with katana

katana finds nothing exploitable by itself — its payoff is *reach*: the endpoints it uncovers that manual browsing and the sitemap miss. The table reads "what katana surfaces → what you attack it with → typical severity of the eventual finding → how likely the lead is already taken."

| Finding class | katana feature that reaches it | Typical severity (of the bug, once attacked) | Dupe risk |
|---|---|---|---|
| Hidden API routes inside JS bundles | `-jc`, `-jsl` | High (BOLA/authz on undocumented APIs) | Low — JS-only endpoints are under-crawled |
| Secrets/keys in JS | `-jsl`, `-kb-secrets` | High–Critical (live keys) | Medium |
| Stale/backup paths in known files | `-kf all` (robots, sitemap) | Medium–High (exposed admin, backups) | Medium |
| SPA XHR/API surface | `-headless -xhr-extraction` | High (the real API behind the SPA) | Low–Medium |
| Authenticated-only endpoints | `-H` / `-al` / `-cwu` + logged/out diff | High (privileged routes) | Low |
| Parameterized URLs for injection | `-fx` + `-mr '='` → `gf`/`dalfox` | Medium–High (XSS, SQLi, redirect) | High — everyone fuzzes these |
| Form targets (CSRF/injection) | `-fx` form extraction | Medium | Medium |
| New endpoints shipped since last crawl | repeat crawl + `-jsonl` diff | varies — freshest code | **Lowest** |

The two lowest-dupe rows — JS-only endpoints and freshly shipped routes — are where crawling actually beats the field. The parameterized-URL row is the highest dupe risk precisely because katana makes it trivial; the edge there is scope and depth, not the crawl itself.

Concretely, the JS-bundle play is the one to lean on: run `-jc` (and `-jsl` on the heavy bundles) to pull every endpoint string out of the compiled JavaScript, then grep the results for `/api/`, `/internal/`, `/admin/`, `/v1/`..`/v3/`, and verb-shaped paths (`/export`, `/import`, `/sync`, `/debug`). Front-end code routinely references back-end routes that are live but undocumented, unlinked, and un-sitemapped — the developer wired them for a feature, forgot to guard them, and no automated scanner that only follows rendered links will ever reach them. That gap is the crawler's real contribution; everything downstream is ordinary testing of an ordinary endpoint.

## Command recipes

Every recipe below uses the low-volume settings a program expects. The defaults to override every time: **`-rl` 150→10, `-c` 10→5**, and always set `-fs` and a `-ct` cap.

| Flag | katana default | Bounty-safe starting point | Why |
|---|---|---|---|
| `-rl` / `-rate-limit` | 150 req/s | 10 (5 if fragile) | 150/s reads as a DoS; trips WAF/rate limits |
| `-rlm` / `-rate-limit-minute` | off | set when the program states a ceiling | enforces an exact per-minute budget |
| `-c` / `-concurrency` | 10 | 5 | URLs crawled in parallel per target |
| `-p` / `-parallelism` | 10 | 2–5 | targets crawled in parallel from a list |
| `-rd` / `-delay` | off | 1 (strict/fragile targets) | fixed gap between requests |
| `-d` / `-depth` | 3 | 3–4 (`-kf` needs ≥3) | deeper = more surface, more volume |
| `-ct` / `-crawl-duration` | off | 10–15m | hard stop against crawl explosions |
| `-fs` / `-field-scope` | `rdn` | explicit scope regex | default can be wider than the program allows |

```bash
# 1. Gentle scoped baseline crawl (start here on any new target)
katana -u https://app.example.com -fs "$SCOPE" -d 3 \
  -rl 10 -c 5 -ct 10m -jsonl -o baseline.jsonl
# -> one JSON record per endpoint: {timestamp, request{method,endpoint,raw}, response{status_code,...}}
#    at ~10 req/s. Watch the console; if the app 429s, drop to -rl 5 or add -rd 1 (1s delay).
```

```bash
# 2. Deep discovery: JS + known files (the high-value, low-dupe pass)
katana -u https://app.example.com -fs "$SCOPE" -d 4 \
  -jc -kf all -rl 10 -c 5 -ct 15m -jsonl -o deep.jsonl
# -> adds endpoints parsed out of .js bundles and listed in robots.txt / sitemap.xml.
#    -kf needs depth >= 3; keep -d at 4 here.
```

```bash
# 3. Parameterized URLs only, with forms, ready for injection tooling
katana -u https://app.example.com -fs "$SCOPE" -d 3 -rl 10 -c 5 \
  -fx -mr '\?|=' -jsonl -o params.jsonl
# -> -fx pulls form/input/textarea/select elements into the JSONL;
#    -mr '\?|=' keeps only URLs carrying query parameters. Feed to dalfox/gf.
```

```bash
# 4. Headless SPA crawl (only after standard mode under-returns)
katana -u https://spa.example.com -fs "$SCOPE" -headless -no-sandbox \
  -system-chrome -xhr-extraction -d 3 -rl 10 -c 5 -ct 10m -jsonl -o spa.jsonl
# -> captures DOM links + XHR. No -sr here: store-response is unsupported in headless.
```

```bash
# 5. Store full request/response for evidence (standard mode only)
katana -u https://app.example.com -fs "$SCOPE" -d 2 -rl 5 -c 3 \
  -sr -srd ./responses -jsonl -o evidence.jsonl
# -> -sr writes each full HTTP request+response under ./responses (-srd) for the report.
```

```bash
# 6. Rate-safe for a strict program: minute budget + inter-request delay
katana -u https://app.example.com -fs "$SCOPE" -d 3 \
  -rlm 120 -rd 1 -c 3 -ct 10m -jsonl -o slow.jsonl
# -> -rlm caps requests PER MINUTE (120/min = 2/s); -rd 1 adds a 1s delay between requests.
#    Use when the program specifies a ceiling or the target is fragile.
```

```bash
# 7. Crawl THROUGH Burp/ZAP so the proxy sitemap fills passively
katana -u https://app.example.com -fs "$SCOPE" -d 3 -rl 10 -c 5 \
  -proxy http://127.0.0.1:8080 -jsonl -o viaproxy.jsonl
# -> every request routes through the intercepting proxy; its sitemap/history now holds
#    the full crawl, ready for manual testing in-tool. -proxy accepts http or socks5.
```

```bash
# 8. Exclude internal ranges and CDNs; resume an interrupted crawl
katana -u https://app.example.com -fs "$SCOPE" -d 3 -rl 10 -c 5 \
  -exclude cdn,private-ips -jsonl -o safe.jsonl
# -> -exclude (-e) drops CDN hosts and RFC1918/private IPs from the crawl — avoids
#    wandering toward internal infrastructure. A killed run leaves resume.cfg; relaunch
#    with -resume resume.cfg to continue where it stopped.
```

```bash
# 9. Keep only interesting responses with the condition DSL
katana -u https://app.example.com -fs "$SCOPE" -d 3 -rl 10 -c 5 \
  -mdc 'status_code == 200' -jsonl -o live200.jsonl
# -> -mdc (match-condition) / -fdc (filter-condition) take DSL expressions over the
#    response, e.g. 'status_code == 200' or 'contains(to_lower(technologies), "php")'.
#    Filter noise at the crawler instead of post-processing a huge JSONL.
```

```bash
# 10. Build a per-host wordlist of paths/params for later fuzzing
katana -u https://app.example.com -fs "$SCOPE" -d 3 -rl 10 -c 5 \
  -sf path,key -jsonl -o crawl.jsonl
# -> -sf/-store-field writes per-host field files under katana_field/ (here: discovered
#    paths and parameter keys) — an instant, target-specific wordlist for ffuf/gf.
```

Field/output control worth knowing: `-field`/`-f` is **deprecated** in favor of `-output-template`; prefer JSONL (`-jsonl`/`-j`) and post-process with `jq`. There is **no `-om jsonl` flag** — JSONL is produced by `-jsonl`/`-j` alone. `-match-regex`/`-mr` and `-filter-regex`/`-fr` act on the output URL string; `-extension-match`/`-em` and `-extension-filter`/`-ef` key on file extension (e.g. `-ef png,jpg,css,woff` to drop static assets from the lead list). Crawl strategy is `-strategy`/`-s` (`depth-first` default, or `breadth-first` to get broad coverage before going deep — often better under a tight `-ct` cap).

### Reading the JSONL output

Each `-jsonl` record is one discovered endpoint. The shape:

```json
{"timestamp":"...","request":{"method":"GET","endpoint":"https://app.example.com/api/v2/users","raw":"GET /api/v2/users ..."},"response":{"status_code":200,"headers":{...},"technologies":["React","Nginx"],"body":"...","raw":"..."}}
```

`jq` turns that into exactly the slice each downstream stage wants:

```bash
jq -r '.request.endpoint' crawl.jsonl | sort -u                       # bare URL list
jq -r 'select(.response.status_code==200) | .request.endpoint' crawl.jsonl   # live only
jq -r 'select(.request.endpoint|test("\\?")) | .request.endpoint' crawl.jsonl # parameterized
jq -r '[.response.status_code,.request.method,.request.endpoint]|@tsv' crawl.jsonl  # triage table
jq -r '.response.technologies[]?' crawl.jsonl | sort | uniq -c | sort -rn    # tech census
```

Add `-omit-body`/`-ob` and `-omit-raw`/`-or` when you only need metadata — the files shrink dramatically and still carry URL, status, and technologies. Reserve the full body/raw capture for the hosts you are actually reporting on.

## Pitfalls, noise and scope discipline

**Scope leakage is the fastest way to a ban.** katana's default scope is the registered root domain of the seed (`rdn`), which can be *wider* than the program allows (it will crawl sibling subdomains). Always pass an explicit `-fs` regex built from the exact in-scope list. **Never use `-no-scope`/`-ns`** on a bounty target — it disables scoping and will crawl wherever links lead. `-display-out-scope`/`-do` only *shows* external URLs it found; it does not crawl them, and those URLs are recon leads to check against scope, not targets.

**`-automatic-form-fill`/`-aff` submits forms** — it is experimental and will POST data: password changes, comment creation, "delete account," invite emails, support tickets. On an authenticated crawl this means real state changes and real emails to real people. Leave it off unless the program explicitly permits active form submission, and even then do it in a throwaway account. The same caution applies to any crawler following state-changing `GET` links (logout, delete, unsubscribe) — scope the crawl and watch what it hits.

**`-kb-validate-secrets` sends live traffic.** The knowledge-base secret validator makes real API calls to confirm a discovered key works. That is outbound traffic to third parties (AWS, Stripe, etc.), often outside program scope and potentially billable to the victim. Collect secrets with `-kb-secrets` if you must, but validate manually and only against in-scope services.

**The default rate is loud.** 150 req/s against a small target looks like a DoS and can trip WAFs, rate limiters, and the program's abuse detection — getting your crawl (and account) flagged. Cap it. If you see HTTP 429/503, stop, lower `-rl` or add `-rd`, and resume. Headless mode multiplies load further.

**Headless gotchas.** `-store-response`/`-sr` is silently unsupported in headless mode, so don't rely on it for evidence there. `-no-sandbox` is needed for root/container contexts but weakens the browser sandbox — run headless crawls in a disposable VM/container, not your host. `-jsluice` and large headless crawls are memory-hungry; a `-ct` cap also protects your own box.

**Dry-run the invocation on one host first.** Before launching a scope regex and rate profile across a whole program, point the exact command at a single in-scope host with `-silent` off and watch the first page or two of output: confirm it stays on allowed hosts, respects the rate you set, and is not already being 403'd. A ten-second sanity check beats discovering mid-run that `-fs` matched nothing (empty output) or everything (`-ns` left on by accident). `-health-check`/`-hc` confirms the tool and its dependencies are wired up.

**Crawl explosions on infinite URL spaces.** Calendars, faceted-search filters, pagination, and session IDs baked into URLs generate effectively unlimited links — a naive crawl can spend your entire rate budget on `?page=99999` and never reach real content. Contain it with `-ct`/`-crawl-duration` (hard time cap), `-max-domain-pages`/`-mdp` (cap pages per host), `-filter-similar`/`-fsu` (drops near-duplicate URLs), and `-ignore-query-params`/`-iqp` (collapse URLs differing only in query values). On a suspected infinite space, `breadth-first` strategy (`-s breadth-first`) gets you broad coverage before the cap hits.

**WAF false blocks skew coverage.** When a target's WAF fingerprints the default Go HTTP/TLS client and serves blanket 403s, your crawl under-reports and you miss real surface — a false negative, not a clean result. `-tls-impersonate`/`-tlsi` (experimental) presents a browser-like TLS ClientHello so the authorized crawl is not misclassified as a bot; treat it as a compatibility measure for a legitimate test, document that you used it, and still honor the program's rate rules. Tune `-timeout` and `-retry` up a little on flaky targets rather than hammering with retries.

**Output hygiene.** Deduplicate before fanning out (`sort -u`, or tools like `uro`); katana can emit many near-identical URLs (pagination, tracking params). `-ignore-query-params`/`-iqp` collapses URLs that differ only in query values. Feeding un-deduplicated output into `nuclei`/`dalfox` multiplies downstream request volume — the scope discipline has to hold across the whole pipeline, not just the crawl. For the diff-over-time workflow, write each crawl to a dated file (`-o crawl-$(date +%F).jsonl`) or use `-no-clobber`/`-ncb` so a repeat run cannot silently overwrite the snapshot you need to diff against.

## Reporting evidence

katana itself rarely *is* the finding, so capture evidence in two layers: the discovery trail (how you found the endpoint) and the exploitation proof (what the endpoint does). For the discovery trail:

- **The exact katana invocation**, including `-fs`, `-rl`, `-c`, and `-d`, so the triager can see the crawl was scoped and rate-limited. This doubles as your proof of good conduct if volume is ever questioned.
- **The JSONL record** for the endpoint (`-jsonl` output): it carries `timestamp`, the full `request` (method, endpoint, raw bytes) and `response` (status_code, headers, detected technologies, body, raw). One record is a compact, verifiable provenance for "this URL exists and returned this."
- **The stored request/response pair** from `-sr`/`-srd` (standard mode) when you need the full on-the-wire bytes in the report.
- **For JS-sourced endpoints**, note the specific bundle file the path came from (`-jc` found it there) — "endpoint `/api/internal/v2/users` referenced in `app.a1b2c3.js:4412`" is far stronger than a bare URL.

Then attach the exploitation step separately (the `curl`/`dalfox`/`nuclei` PoC against the discovered endpoint, expected vs. actual). Redact tokens and any real user data from stored responses, delete throwaway accounts and submitted test data when done, and never include more of a response body than the proof needs. Frame the impact on the *endpoint's* behavior; "katana found it" is the provenance, not the vulnerability.

A discovery-trail block, as it reads in a report:

```
Discovery: the endpoint below is not in the sitemap, not linked from any
rendered page, and only appears as a string inside a JavaScript bundle.

  $ katana -u https://app.example.com -fs '(^|\.)example\.com$' \
      -jc -d 4 -rl 10 -c 5 -jsonl -o deep.jsonl
  $ jq -r 'select(.request.endpoint|test("/internal/")) | .request.endpoint' deep.jsonl
  https://app.example.com/api/internal/v2/users/export

  Source bundle: /static/app.9f2c1b.js (referenced via -js-crawl)
  Crawl rate: 10 req/s, concurrency 5, scoped to *.example.com

Exploitation: [separate curl PoC showing the endpoint returns other
tenants' records without an ownership check — see BOLA finding above.]
```

The crawl line doubles as proof the discovery was scoped and rate-limited — useful if the triager questions how an undocumented endpoint was found or whether testing stayed in bounds.

## References

- katana README (usage, scope, headless, filters, knowledge-base, output) — https://github.com/projectdiscovery/katana
- ProjectDiscovery docs, katana usage — https://docs.projectdiscovery.io/tools/katana/usage
- ProjectDiscovery docs, katana running/overview — https://docs.projectdiscovery.io/tools/katana/running
- Companion pipeline tools in this vault: [[subfinder]], [[httpx]], [[dalfox]], [[nuclei]]

*Research supplement added 2026-11-15, grounded in the projectdiscovery/katana README and the ProjectDiscovery docs (tools/katana usage + running), flag names verified against those sources (model: claude-opus-4-8).*
