---
name: "wapiti"
categories:
  - blackarch-webapp
version: "3.3.2.r10.g1e81698"
homepage: "https://github.com/wapiti-scanner/wapiti"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: python
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "wapiti-scanner/wapiti"
github-stars: 1905
github-pushed: "2026-10-05"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 179
gh-activity-checked: "2026-11-09"
---

# wapiti

> Black-box web application vulnerability scanner with one attack module per vulnerability class and resumable sqlite3 sessions.

## What it's for

Wapiti crawls a deployed web application — links, forms, URLs extracted from JavaScript, optionally a headless Firefox — and then attacks the discovered scripts with payloads, working like a fuzzer without ever reading source code. Each vulnerability class is an attack module (sql, xss, exec, file, xxe, crlf, ssrf, redirect, log4shell, spring4shell, buster, nikto, cms, wp_enum, takeover, cookieflags, http_headers, and more) that can be listed, selected, or skipped. Scans suspend and resume through sqlite3 session files, and results are emitted as reports in HTML, XML, JSON, CSV, TXT, or Markdown. It is black-box only: it does not analyze source code. The companion `wapiti-getcookie` tool captures a session cookie in a GUI to feed authenticated scans.

## Typical use cases

### Run a full default scan

Crawl and attack a target with the default module set.

```
wapiti -u https://example.com/
```

Outcome: crawls the target, runs the default attack modules, and prints found vulnerabilities to stdout colorized by severity; the crawl and attack state is stored in a sqlite3 session file so a re-run resumes where it left off.

### Scan with only selected modules

Restrict the attack phase to specific vulnerability classes.

```
wapiti -u https://example.com/ -m sql,xss,exec
```

Outcome: only the named modules' payloads are sent; everything else in the crawl/attack pipeline is unchanged. Use `wapiti --list-modules` first to discover valid module names.

### Discover available attack modules

List every attack module the installed version ships.

```
wapiti --list-modules
```

Outcome: prints each module name with a short description (e.g. `sql`, `permanentxss`, `log4shell`, `wapp`), which is the vocabulary for the `-m` option.

### Generate a report in a chosen format

Produce a shareable report instead of relying on console output.

```
wapiti -u https://example.com/ -o wapiti-report.html -f html
```

Outcome: writes `wapiti-report.html` at the end of the attack phase; `-f` accepts `csv`, `html`, `json`, `md`, `txt`, or `xml`.

### Scan through a proxy

Route all crawl and attack traffic through an intercepting proxy.

```
wapiti -u https://example.com/ -p http://127.0.0.1:8080
```

Outcome: every request Wapiti makes passes through the proxy (e.g. Burp Suite), letting you watch and tweak payloads; `--tor` routes through the Tor network instead.

### Scan behind HTTP authentication

Give Wapiti credentials for HTTP-protected areas.

```
wapiti -u https://example.com/ -a user:Password1 --auth-method basic
```

Outcome: Wapiti authenticates with Basic credentials; `--auth-method` also accepts `digest` and `ntlm`. For form-based logins use the `--form-*` options or a `--form-script` for complicated cases.

### Bound the crawl scope

Keep the crawler inside a subset of the target.

```
wapiti -u https://example.com/app/ --scope folder
```

Outcome: crawling is restricted to the chosen scope; accepted values are `url`, `page`, `folder`, `subdomain`, `domain`, and `punk`.

## Bug bounty workflow with wapiti

Wapiti is a black-box fuzzer: it crawls a live app, then fires one attack module per vulnerability class at every parameter it found. On a bug bounty program that makes it a **lead generator**, not a reporting tool — its output is a queue of "this parameter reflected my payload" signals that you must reproduce by hand before a word of it reaches a report. The discipline below keeps the scan inside program rules (automated-scanning clauses, volume limits, no third-party data egress) and keeps the findings defensible.

The single most important fact to internalise before the first scan: **Wapiti has no request-rate or request-delay flag.** There is no `--delay`, `--rate`, or `--throttle`. Volume is governed only by concurrency (`--tasks`), the number of URL patterns attacked as parameter counts grow (`-S/--scan-force`), crawl breadth (`--scope`, `-d/--depth`, `--max-links-per-page`, `--max-files-per-dir`), the module set (`-m`), and the time caps (`--max-scan-time`, `--max-attack-time`). If a program caps request rate, you throttle Wapiti by routing it through a rate-limiting proxy, not with a Wapiti flag — see Pitfalls.

### Phase 0 — Authorization, scope, and a throttled baseline

Goal: confirm the program permits automated scanning at all, and build a command skeleton that cannot run away from you.

- Read the program's rules of engagement for an **automated-scanning / scanner clause**. Many programs forbid or rate-limit fuzzers; some require a dedicated test account or a custom `User-Agent` so their SOC can distinguish you from an attacker. Wapiti's default `User-Agent` is a **Tor Browser string** (it deliberately hides itself) — set an identifying one with `-A` when the program asks for attribution.
- Decide the egress posture. The `ssrf`, `log4shell`, and `takeover` modules phone home to a **third-party endpoint the Wapiti author runs** (`http://wapiti3.ovh/`, DNS `dns.wapiti3.ovh`) to catch out-of-band callbacks. Running them untouched sends your target's hostnames and injected markers to infrastructure you do not control — a data-handling problem on most programs. Leave those modules out of the default run, or self-host the endpoint (Phase 4).

```
wapiti -u https://example.com/ \
  -p http://127.0.0.1:8080 \
  --scope folder \
  --tasks 1 \
  -S paranoid \
  -d 3 \
  -A "bugbounty-researcher (hackerone.com/your-handle)" \
  --verify-ssl 1 \
  -m "" \
  -v 2 -o wapiti-baseline.html -f html
```

Outcome: a crawl-only pass (`-m ""` runs **no attack modules**) through Burp (`-p`), confined to the URL folder (`--scope folder`), single-tasked (`--tasks 1`), with the most conservative attack-pattern budget (`-S paranoid`). Nothing is attacked yet; you are building the site map and watching every request land in Burp's proxy history. Note `--verify-ssl 1` — Wapiti **disables certificate validation by default** (`--verify-ssl 0`), so turn it on unless the target legitimately uses a self-signed cert.

### Phase 1 — Passive-first mapping and fingerprinting

Goal: know the surface and the stack before you throw a single payload.

```
wapiti -u https://example.com/ -m "wapp" --scope domain -d 3 \
  -p http://127.0.0.1:8080 --tasks 2 -o wapiti-wapp.json -f json
```

Outcome: `wapp` is **not an attack module** — it fingerprints technologies (server, framework, CMS, JS libraries) and looks up known CVEs for the detected versions, using the Wappalyzer-style database refreshed by `--update`. The JSON report becomes your version-matching worksheet: anything it flags with a CVE is a lead to verify independently, and the tech stack tells you which attack modules are worth running (no point running `wp_enum` on a non-WordPress target, or `spring4shell` off a non-Java stack).

Review the baseline crawl from Phase 0 in Burp and in `wapiti-baseline.html`: confirm the crawler stayed in scope, did not follow the logout link (exclude it in Phase 2 with `-x`), and reached the parameters you care about. If the app is a SPA whose routes load via XHR, the default crawler misses them — re-crawl with `--headless hidden --wait 2` so a headless Firefox executes the JavaScript first (slower; narrow the scope to compensate).

### Phase 2 — Targeted, authenticated attack

Goal: attack only the parameters that matter, authenticated, with the high-signal modules — never `-m all`.

Capture a session first. For a form login, let Wapiti drive it:

```
wapiti -u https://example.com/app/ \
  --form-user "testuser@example.com" --form-password "BBPass!23" \
  --form-url https://example.com/login \
  -x https://example.com/logout \
  --scope folder -d 4 --tasks 2 -S polite \
  -p http://127.0.0.1:8080 --verify-ssl 1 \
  -m "sql,timesql,xss,permanentxss,exec,file,xxe,ldap,crlf" \
  -v 2 --max-attack-time 600 \
  -o wapiti-attack.html -f html -dr 1
```

Outcome: Wapiti logs in via the form, excludes the logout URL (`-x`, so it does not kill its own session), and attacks only the named high-value classes. `-dr 1` (detailed report, level 1) **embeds the HTTP responses of successful attacks** into the HTML — the evidence you will need for Phase 3. `--max-attack-time 600` caps each module at ten minutes so a pathological parameter cannot run the scan for hours. `-S polite` keeps the per-URL pattern budget modest.

Alternatives for other login shapes: `-c cookies.json` to replay a cookie jar captured with `wapiti-getcookie`; `-C "SESSION=abc123;csrf=..."` to pass a raw cookie string inline; `--jwt <token>` for bearer-style APIs; `--auth-user`/`--auth-password` with `--auth-method basic|digest|ntlm` for HTTP auth; `--form-script myauth.py` for multi-step or JS-heavy logins the built-in form filler cannot handle; `-sf login.side` to replay a Selenium IDE recording.

For an API with an OpenAPI/Swagger document, feed it directly instead of relying on the crawler:

```
wapiti -u https://api.example.com/ --swagger https://api.example.com/openapi.json \
  -m "sql,xss,exec,file,ssrf" --scope url --tasks 2 \
  -p http://127.0.0.1:8080 --verify-ssl 1 -o wapiti-api.json -f json -dr 1
```

Outcome: `--swagger` parses the spec, synthesises the documented requests (paths, methods, parameters, body shapes), and adds them to the crawl queue — so Wapiti attacks endpoints a link-crawler would never discover.

### Phase 3 — False-positive verification (mandatory)

Goal: turn each Wapiti "Anomaly"/"Vulnerability" entry into a hand-reproduced finding, or discard it.

Wapiti reports by injecting payloads and pattern-matching the response (reflected marker, SQL error string, time delay for `timesql`, OOB callback for `ssrf`). Every class has a false-positive mode: a reflected marker that is HTML-encoded is not XSS; a database error string in a WAF block page is not SQLi; a slow response under load looks like a time-based injection. **Do not forward the HTML report.** For each entry:

1. Open the report entry — it gives the exact URL, parameter, method, and the injected payload (with `-dr 1`, the full request/response).
2. Replay that one request by hand in Burp Repeater or `curl`, confirm the vulnerable behaviour, and minimise the payload to the smallest thing that still works.
3. Prove impact, not just reflection: for XSS, confirm the payload executes in a browser (unencoded, in an executable context); for SQLi, extract one harmless proof (`@@version`, `current_user`) rather than dumping data; for `file`, read one benign in-scope file.
4. Discard anything you cannot reproduce manually. A scanner hit you cannot re-trigger is noise, and reporting it gets you a reputation hit.

### Verification cheatsheet — what each module keys on, and its false-positive tell

Wapiti decides a payload "worked" with a per-module heuristic. Knowing the heuristic tells you exactly what to re-check by hand and where each class lies to you.

| Module | How Wapiti flags a hit | Classic false positive | Manual confirmation |
|---|---|---|---|
| `sql` | Known DB error strings appear in the response after injecting quote/syntax-breaking payloads | Error string lives in a static WAF/error page, or is reflected input, not a real DB error | Reproduce with a boolean pair (`' AND 1=1-- ` vs `' AND 1=2-- `); extract `@@version`/`current_user` |
| `timesql` | Response time rises after a time-delay payload (`SLEEP`/`WAITFOR`) | Network jitter or a slow endpoint under load reads as a delay | Re-run the delay and a zero-delay control several times; the delay must track the injected seconds |
| `xss` | The injected marker is reflected unencoded in the response | Marker reflected inside an attribute/text node that is actually encoded, or in a non-executing context | Load it in a real browser; confirm script execution, not just reflection |
| `permanentxss` | A marker injected earlier resurfaces on a later crawled page (stored) | Marker stored but rendered encoded, or in an admin-only view you can't reach as a user | Confirm execution in the context where a victim would see it |
| `exec` | OS-command output or a known error pattern appears in the response | Payload fragment echoed back as plain text | Re-run with a benign command (`id`, `whoami`) and confirm the *output*, not the echo |
| `file` | Contents of a probed path (e.g. `/etc/passwd` signature) appear in the response | A WAF block page or a 200 error template that happens to contain matching text | Read one benign in-scope file and diff against a known-good baseline |
| `xxe` | An injected external entity resolves (often via the OOB endpoint) | Parser error mistaken for entity resolution | Correlate with your self-hosted endpoint log (Phase 4) |
| `ssrf` | The target calls the configured endpoint back | A callback caused by an unrelated feature, or a `wapiti3.ovh` hit you can't inspect | Self-host the endpoint; match the marker in the request to the module's injection |
| `crlf` | Injected header/line-break reflected in response headers | Proxy or app normalises the response, reflecting the literal payload in a body | Confirm an actual new header line in the raw response, not body text |
| `redirect` / `https_redirect` | A `Location`/redirect follows injected URL/host | In-scope redirect that only accepts same-origin targets | Confirm redirect to an *external* attacker-controlled host |

The pattern across the table: **reflection is not exploitation, an error string is not an injection, and a slow response is not a time-based vulnerability.** Every one needs the control request.

### Phase 4 — Out-of-band modules (deliberate, self-hosted)

Goal: run `ssrf`/`log4shell`/`takeover` only when in scope, without leaking to a third party.

Stand up your own collaborator-style listener (Interactsh, Burp Collaborator, or a simple logging host you control on an authorized IP) and point Wapiti at it:

```
wapiti -u https://example.com/ \
  -m "ssrf,log4shell" \
  --endpoint http://198.51.100.10:8080/ \
  --dns-endpoint oob.example-collab.net \
  --scope folder --tasks 1 -S paranoid \
  -p http://127.0.0.1:8080 --verify-ssl 1 \
  -o wapiti-oob.json -f json -dr 1
```

Outcome: Wapiti injects markers that make the target call *your* endpoint instead of `wapiti3.ovh`. `--endpoint` sets both the internal and external endpoint to the same value; use `--internal-endpoint` (where Wapiti reads results) and `--external-endpoint` (what the target reaches) separately when you are behind NAT. `--dns-endpoint` is the domain the `log4shell` module uses for DNS-based callbacks. An entry here means a real OOB interaction landed on your listener — correlate the hit in your listener's log with the Wapiti report before claiming it.

### Phase 5 — Report

Carry each verified finding into the program's template with the manual PoC, not the scanner output. Keep the Wapiti report as internal provenance (methodology, coverage), and cite the module and version in your notes so a re-test is reproducible.

## Where the money is with wapiti

Priority is by payout-per-effort on a typical web program: injection and RCE classes first (low dupe, high severity), misconfiguration classes last (high dupe, often out of scope or already known).

| Priority | Finding class | Module(s) | Typical severity | Dupe / scope risk |
|---|---|---|---|---|
| 1 | SQL injection (error- and time-based) | `sql`, `timesql` | High–Critical | Low on custom endpoints; high on known CMS |
| 2 | OS command execution | `exec` | Critical | Low — rare and high-value |
| 3 | Path traversal / local file read | `file` | High | Medium |
| 4 | XXE | `xxe` | High | Low–Medium |
| 5 | SSRF (OOB) | `ssrf` | High | Low — but needs self-hosted endpoint (Phase 4) |
| 6 | Unrestricted file upload | `upload` | High–Critical | Low |
| 7 | LDAP injection | `ldap` | High | Low |
| 8 | Log4Shell / Spring4Shell | `log4shell`, `spring4shell` | Critical | Low, but mostly patched — version-gated |
| 9 | Stored & reflected XSS | `permanentxss`, `xss` | Medium–High | Medium–High |
| 10 | CRLF / HTTP response splitting | `crlf` | Medium | Medium |
| 11 | Subdomain takeover | `takeover` | High | Medium (often already claimed) |
| 12 | Open redirect | `redirect`, `https_redirect` | Low–Medium | **Very high — frequently out of scope** |
| 13 | CSRF | `csrf` | Low–Medium | High |
| 14 | Known-vuln files / backups / dirs | `nikto`, `backup`, `buster`, `htaccess` | Info–Medium | High |
| 15 | Security-header / cookie / CSP gaps | `http_headers`, `cookieflags`, `csp` | Info–Low | **Very high — usually informational-only** |
| 16 | Tech + CVE fingerprint (not an attack) | `wapp` | Info (feeds other work) | n/a |

Read the bottom of the table as a warning list: `http_headers`, `cookieflags`, `csp`, `redirect`, and `csrf` findings are the ones programs reject as duplicates or known-issues most often. They are worth keeping in a scan for completeness and chaining, but a lone "missing `X-Frame-Options`" report is usually a waste of a submission. The top of the table is where Wapiti earns its place: a confirmed `sql`/`exec`/`file`/`xxe` hit on a custom endpoint is a real payout.

## Command recipes

Each block is annotated with the low-volume setting that keeps it program-safe. Remember throttling comes from concurrency + attack-budget + scope, never a rate flag.

**Discover the exact module vocabulary your installed version ships.**

```
wapiti --list-modules
```

Expected output: each module name with a one-line description (`sql`, `timesql`, `xss`, `permanentxss`, `exec`, `file`, `xxe`, `ssrf`, `crlf`, `ldap`, `upload`, `redirect`, `https_redirect`, `log4shell`, `spring4shell`, `shellshock`, `takeover`, `backup`, `buster`, `htaccess`, `nikto`, `cms`, `wp_enum`, `http_headers`, `cookieflags`, `csp`, `csrf`, `methods`, `htp`, `network_device`, `printer`, `ssl`, `wapp`). Use these exact names with `-m`; do not guess (`drupal_enum` and `wordpress` are not valid — Drupal is `--cms drupal` under the `cms` module, and WordPress is `wp`).

**Crawl only, no attacks — map the surface at minimum volume.**

```
wapiti -u https://example.com/ -m "" --scope folder -d 3 --tasks 1 \
  -p http://127.0.0.1:8080 -o map.html -f html
```

Expected: a full report of discovered URLs and forms with **zero attack payloads sent**. The safest possible first contact with a target.

**Attack a single request only (surgical, lowest footprint).**

```
wapiti -u "https://example.com/item?id=5" --data "id=5" \
  -m "sql,timesql" --scope url --tasks 1 -dr 1 -v 2
```

Expected: `--data` makes Wapiti attack exactly one POST request to the `-u` URL and nothing else — no crawl. `--scope url` keeps it to that single page. Ideal for re-testing one parameter you already suspect, or when the program forbids broad crawling.

**Tune volume down hard for a rate-sensitive program.**

```
wapiti -u https://example.com/ -m "sql,exec,file" \
  --tasks 1 -S paranoid --scope page -d 2 \
  --max-links-per-page 20 --max-files-per-dir 10 --max-parameters 20 \
  --max-scan-time 1800 -p http://127.0.0.1:8080
```

Expected: `--tasks 1` serialises requests; `-S paranoid` caps attacked URL patterns to the minimum (roughly 30 patterns for a 1-parameter URL, down to 1 pattern once a URL has 3+ parameters, versus `normal`'s 147); `--scope page` attacks only the start page's parameters; `--max-scan-time 1800` is a hard 30-minute wall. This is the shape to use when "don't hammer us" is in the rules.

**Increase injection reach for query-string-parsing endpoints.**

```
wapiti -u https://example.com/cgi-bin/ -m "sql,exec" -l 2 --scope folder
```

Expected: `-l 2` (level 2; default is 1) re-enables injecting payloads into the query string of URLs that have **no declared parameters** — useful for CGIs and routers that parse the raw query string themselves. It increases request volume, so pair it with a tight scope.

**Resume an interrupted scan instead of restarting it.**

```
wapiti -u https://example.com/ -m "sql,xss,file" --tasks 2
# ... interrupted (Ctrl-C, timeout) ...
wapiti -u https://example.com/ -m "sql,xss,file" --tasks 2   # same command resumes
```

Expected: since 3.0.0 Wapiti stores crawl state, discovered URLs, and per-module attack status in a SQLite session keyed by target+scope, so re-running the **same** command continues where it stopped — no re-crawl, no re-attacking of already-tested parameters. Related session controls: `--skip-crawl` (attack only already-known URLs, don't resume crawling), `--resume-crawl` (keep crawling after an attack-phase restart), `--flush-attacks` (drop recorded vulns and attack status but keep the crawl), `--flush-session` (wipe everything for the target and start clean).

**Capture an authenticated session once, reuse it for scanning.**

```
wapiti-getcookie -u https://example.com/login -c cookies.json \
  --form-data 'login=testuser@example.com&password=BBPass!23&submit=Login' \
  -p http://127.0.0.1:8080
# then scan with the saved jar:
wapiti -u https://example.com/app/ -c cookies.json \
  -x https://example.com/logout -m "sql,xss,file" --scope folder -dr 1
```

Expected: `wapiti-getcookie` submits the login (or, without `--form-data`, prompts interactively for each form field), then writes the server-set cookies to a Wapiti JSON file; `wapiti -c cookies.json` replays them. This is the robust path when Wapiti's built-in `--form-*` login fails on a complex form. `wapiti-getcookie` also supports `--auth-user`/`--auth-password`/`--auth-method`, `-H`, `-A`, `--headless hidden`, and `--tor`. Note its default `User-Agent` is a modern Firefox string (not Tor Browser like `wapiti` proper), so the capture and the scan may present different agents unless you set `-A` on both.

**Produce a triage-ready report with embedded evidence.**

```
wapiti -u https://example.com/ -m "sql,xss,exec,file,xxe" \
  --scope folder -dr 1 -o report.html -f html
```

Expected: `-f` accepts `csv`, `html`, `json`, `md`, `txt`, `xml`. `-dr 1` embeds the HTTP responses of successful attacks into the report; `-dr 2` additionally stores requests and responses for *every* crawled page (large, but complete). Use `json` when you want to post-process findings; `html` for human triage.

## Chaining wapiti into a bug-bounty pipeline

Wapiti is strongest as one stage, not the whole engagement. Its crawler is competent but single-host and link-driven; feed it a better surface and consume its output downstream.

**Feed it a discovered URL list.** When you have already enumerated endpoints with a dedicated crawler/collector (katana, gau, hakrawler, a Burp sitemap export), hand them to Wapiti as extra seeds so it attacks paths its own crawl might miss:

```
wapiti -u https://example.com/ -s urls.txt -m "sql,xss,file" --scope url \
  -p http://127.0.0.1:8080 -dr 1
```

Outcome: `-s` takes a UTF-8 file of one URL per line (or a single URL, repeatable) and adds each as a depth-0 start point. Pair it with `--scope url` so Wapiti attacks exactly those URLs' parameters and does not expand the crawl. `-s` is the bridge between your recon tooling and Wapiti's attack engine.

**Feed it the API contract.** For API targets, `--swagger <openapi.json>` (Phase 2) beats crawling outright — it reconstructs documented requests the crawler can't reach. Combine with `-s` for endpoints absent from the spec.

**Export machine-readable, grep downstream.** `-f json -o out.json` gives a structured report; pull just the confirmed-worth-checking entries into your manual queue instead of eyeballing HTML:

```
wapiti -u https://example.com/ -m "sql,exec,file,xxe" -f json -o out.json -dr 1
jq '.vulnerabilities | to_entries[] | select(.value|length>0) | .key' out.json
```

Outcome: lists only the vulnerability classes that produced at least one hit, so you know which sections of the report to verify (Phase 3) before touching the rest.

**Keep Burp in the loop for everything.** Running with `-p http://127.0.0.1:8080` throughout means every Wapiti request lands in Burp's history. When Wapiti flags a parameter, the triggering request is already in Repeater — right-click, replay, minimise. This is the fastest false-positive-verification loop and the reason the proxy flag is in every recipe above.

## Pitfalls, noise and scope discipline

**There is no rate-limit flag — plan for it.** Hunters assume `--tasks 1` makes Wapiti gentle. It serialises concurrency but does not pause between requests, so a deep crawl of a big app still means thousands of fast requests. If the program caps request rate, put a throttling proxy in front: route Wapiti through Burp (`-p http://127.0.0.1:8080`) or `mitmproxy` with a rate-limiting addon, or chain through a tool that enforces a delay. Shrinking the footprint with `-S paranoid`, a narrow `--scope`, a shallow `-d`, `--max-links-per-page`/`--max-files-per-dir`, and `--max-scan-time` is the in-tool mitigation. Say in your report which of these you used.

**`-m all` (and the `all` keyword) is a scope and noise disaster.** It runs every module, including `nikto`, `buster`, `backup`, `shellshock`, `brute_login_form`, and `network_device`/`printer` probes — thousands of requests, many against paths and devices that may be out of scope, plus a login-bruteforce module that can lock accounts. The README itself flags `all` as "not recommended." Always name the modules you want with `-m`.

**The OOB modules leak to a third party by default.** `ssrf`, `log4shell`, and `takeover` use `http://wapiti3.ovh/` / `dns.wapiti3.ovh` — infrastructure run by the project, not you — to detect out-of-band callbacks. Running them as-is sends target identifiers and injection markers off to a host outside the engagement, which violates the data-handling clause of many programs and muddies your evidence (you can't inspect the collaborator log). Always self-host with `--endpoint`/`--external-endpoint`/`--dns-endpoint` (Phase 4), or leave these modules out.

**Certificate validation is off by default.** `--verify-ssl` defaults to `0`. On a bug bounty you want `--verify-ssl 1` so you don't silently scan a man-in-the-middle or a wrong host behind a shared IP. The default-off behaviour also means a TLS-misconfiguration finding won't surface from the crawl — use the `ssl` module (which needs `sslscan` installed) or a dedicated TLS scanner for that.

**The crawler can log itself out and attack destructive endpoints.** Always `-x` the logout URL during authenticated scans, or Wapiti follows it mid-crawl and loses the session. The crawler is also not state-aware: it will happily submit "delete", "cancel order", "send invite", and "change email" forms with its payloads. On a shared or production program this causes real side effects and spam. Use a disposable test account, exclude dangerous paths with `-x`, strip dangerous parameters with `-r`, or mark them untested with `--skip`, and prefer `--data`/`--scope url` surgical runs on sensitive flows.

**Scanner output is not a finding.** Reflected-marker, error-string, and timing heuristics all false-positive. A report built from Wapiti's HTML without the Phase 3 manual reproduction is the fastest way to get marked as a low-quality noise reporter — and some programs ban for it. Every submitted item must have a hand-crafted, minimal PoC.

**Low-severity module output is mostly duplicate.** `http_headers`, `cookieflags`, `csp`, `redirect`, and `csrf` hits are the most-rejected class on mature programs (known issues, accepted risk, or out of scope). Keep them for chaining, not as standalone submissions.

**Default User-Agent hides you.** Wapiti ships a Tor Browser `User-Agent`. If the program wants attributable traffic (many do), set `-A` to an identifiable string with your handle; otherwise your scan looks like hostile anonymised traffic to their SOC.

## Reporting evidence

Capture the following for every verified finding — the first two come straight from Wapiti, the rest you produce by hand in Phase 3:

- **The exact Wapiti report entry**: module name, vulnerability class, target URL, HTTP method, the injected parameter, and the payload Wapiti used. With `-dr 1` the entry already contains the triggering request and the server's response — quote the relevant lines, don't paste the whole report.
- **The scan provenance line**: the full command you ran (modules, scope, scan-force, tasks), Wapiti's version (`wapiti --version`), and the date — so a triager or re-tester can reproduce the discovery conditions.
- **A minimal manual PoC**: a single `curl` or Burp Repeater request/response pair reproducing the issue with the smallest payload that works, labelled *expected vs. actual*. This, not the scanner output, is the body of the report.
- **Proof of impact, bounded**: for SQLi, one benign disclosure (`@@version`, `current_user`) — never a data dump; for `file`, one in-scope benign file read; for `exec`, a harmless command (`id`, `whoami`) output; for XSS, a screenshot or a short clip of the payload executing in a browser in an executable context; for `ssrf`/`log4shell`, the correlated hit from **your** self-hosted listener's log with timestamp.
- **Remediation pointer**: name the class (parameterised queries for `sql`, output encoding + context for `xss`, allowlist + canonicalisation for `file`, disabled external entities for `xxe`) so the triager sees you understand the root cause.
- **Cleanup note**: state that you removed any test data created, and that the account used was a disposable test account — especially important because the crawler may have submitted forms.

Never attach the raw multi-megabyte `-dr 2` report to a submission; keep it as internal evidence and extract only the entry that proves the finding.

## References

- Wapiti homepage — https://wapiti-scanner.github.io/
- Wapiti GitHub repository — https://github.com/wapiti-scanner/wapiti
- Wapiti man page (options reference) — https://github.com/wapiti-scanner/wapiti/blob/master/doc/wapiti.1
- Wapiti man page source (ronn) — https://github.com/wapiti-scanner/wapiti/blob/master/doc/wapiti.ronn
- Wapiti README (module list, requirements) — https://raw.githubusercontent.com/wapiti-scanner/wapiti/master/README.rst
- Wiki: Crawler Limitations and Request Throttling — https://github.com/wapiti-scanner/wapiti/wiki/Crawler-Limitations-and-Request-Throttling
- Wiki: HTTP and Web Form authentication — https://github.com/wapiti-scanner/wapiti/wiki/HTTP-and-Web-Form-authentication
- Wiki: Session management — https://github.com/wapiti-scanner/wapiti/wiki/Session-management

*Research supplement added 2026-11-16, grounded in the Wapiti man page, README.rst, homepage, and project wiki (URLs above); verified against Wapiti 3.3.x (model: claude-opus-4-8). Original note: derived from upstream documentation (README.rst and wapiti.1 manpage).*
