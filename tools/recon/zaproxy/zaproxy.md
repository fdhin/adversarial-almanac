---
name: "zaproxy"
categories:
  - identify
  - web
version: "2.17.0-4"
homepage: "https://www.zaproxy.org/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali]
arch-builddate: "2026-07-02"
build-system: java
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "zaproxy/zaproxy"
github-stars: 16126
github-pushed: "2026-11-06"
github-archived: false
github-issues: 880
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 379
gh-activity-checked: "2026-11-09"
---

# zaproxy

> OWASP ZAP: an intercepting proxy, spider and active/passive web-app scanner usable as a desktop GUI, a headless daemon with an API, or CI-ready packaged scan scripts.

## What it's for

The Zed Attack Proxy (ZAP, maintained by Checkmarx) sits between your browser and the target application: it records every request/response, spiders the app, passively scans traffic as it flows, and can actively attack (injection, misconfiguration and disclosure checks via add-on scan rules). It spans manual pentesting (intercept/replay, fuzzing, session handling, contexts for authenticated scope) and automation (headless daemon with a JSON API; packaged `zap-baseline.py`/`zap-full-scan.py`/`zap-api-scan.py` scripts in the Docker images). Distro packages install the launcher as `zaproxy` — the same script ZAP releases call `zap.sh`. It is a scanner proxy, not a one-shot crawler: it only sees what flows through it unless you point its spider at the target, and active scanning against third-party systems without permission is off-limits by design.

Commands below are verified against the upstream command-line and Docker documentation.

## Typical use cases

### Launch the desktop GUI and intercepting proxy

Start an interactive testing session.

```
zaproxy
```

Outcome: opens the ZAP desktop UI with the intercepting proxy on `127.0.0.1:8080`; point a browser at that proxy, browse the target, and traffic appears in the Sites tree for manual inspection, re-sending and fuzzing.

### Run headless as a daemon with an API

Drive ZAP programmatically or from other tooling.

```
zaproxy -daemon -newsession zap-session -config api.key=Password1
```

Outcome: starts ZAP without a UI, creates a fresh session named `zap-session`, and exposes the REST API on `127.0.0.1:8080` (endpoints under `/JSON/...`) authenticated with the configured key — ready for scripted spider/scan calls. `-suppinfo` prints version, Java and add-on details for troubleshooting.

### Passive baseline scan in CI

Check a site without attacking it — safe even against production.

```
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py -t https://www.example.com
```

Outcome: spiders the target for one minute, waits for passive scanning to finish, and prints one `PASS`/`WARN`/`FAIL`/`INFO` line per rule with rule IDs (e.g. `WARN: Incomplete or No Cache-control [10015]`); exits non-zero on FAILs. No attack traffic is generated.

### Baseline scan with a rules config and report

Make findings gate a pipeline and leave an artifact.

```
docker run -v $(pwd):/zap/wrk:rw -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py -t https://www.example.com -c baseline.conf -r report.html
```

Outcome: `-g gen.conf` generates a starter config listing every rule as WARN — edit copies to set rules to `IGNORE`, `FAIL` or `INFO` and pass it as `-c baseline.conf`; `-r` writes the full HTML report to `report.html` in the mounted directory (`-x`/`-J` produce XML/JSON). The same option set works for `zap-full-scan.py` (adds active scanning — never point it at systems you do not own) and `zap-api-scan.py` (API definitions; `-f openapi` or `swagger`).

### Authenticated scan against a context

Scan behind a login using a ZAP context file.

```
docker run -v $(pwd):/zap/wrk:rw -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py -t https://www.example.com -n example.context -U user
```

Outcome: loads `example.context` (a context defines the in-scope URL patterns plus authentication/session management — export it from the GUI's Session Properties) before spidering, and authenticates as the context-defined user `user` so protected pages are reached.

### Re-run a script against a saved session

Automate post-processing of an existing session file.

```
zaproxy -session /full/path/to/session -script /full/path/to/script.js -cmd
```

Outcome: starts ZAP headlessly, opens the given session, executes the ZAP script (JavaScript/Groovy/Zest) against it and exits when done — the documented pattern for scripted report extraction or custom checks over recorded traffic.

---

## Bug bounty workflow with ZAP

ZAP is the open-source Burp alternative: one process that is both an intercepting proxy and a passive/active scanner. For a bug bounty program that is also its central hazard. The proxy and the **passive scanner** generate no attack traffic and are safe everywhere; the **active scanner** is, in ZAP's own words, "an attack on those targets" and its docs say plainly *"You should NOT use it on web applications that you do not own."* So the whole method below is scope-first: you physically constrain what ZAP can touch **before** you let it crawl or attack, and you only enable the active scanner once the program's rules of engagement authorize automated scanning. Everything here is single-target, throttled, and evidence-oriented.

The discipline in one sentence: **a Context defines where ZAP may go, a Mode defines what ZAP may do there, and a Scan Policy defines how hard it hits — set all three before the first spider run.**

### Phase 0 — Scope lockdown (Context + Mode), before any crawl

Goal: make it impossible for ZAP to leave program scope, then prove it to yourself.

1. **Create a Context** (`Session Properties → Contexts`, or right-click a site → *Include in Context*). A Context is "a way of relating a set of URLs together" — one Context per target application. Scope is defined entirely by Contexts: ZAP's own default is that **"by default nothing is in scope."**
2. **Fill the "Include in Context" panel** with anchored regular expressions. The regexes must **match the whole URL**, and "URLs which don't match any of the regexes will not be included in the context." Anchor them — `https://app\.example\.com/.*` — never a bare `.*example\.com.*`, which also matches `notexample.com.evil.tld` and anything with the string in a query parameter. Use `\Q...\E` to quote literal metacharacters.
3. **Fill the "Exclude from Context" panel** for anything in-scope-by-pattern you must never touch: logout URLs, `/admin/delete`, payment/withdraw endpoints, account-deletion, mass-email triggers, and any path the program lists as out of scope. Exclude regexes only need to cover URLs that *would otherwise match* an include.
4. **Set the Mode** (toolbar dropdown; the choice is persisted between sessions and settable via the API). The four modes:
   - **Safe** — "no potentially dangerous operations permitted." Spidering, active scan, fuzzing, forced browse, breaking/intercepting, and resending are all blocked. Use this for pure passive recon.
   - **Protected** — "you can only perform (potentially) dangerous actions on URLs in the scope." This is the bug-bounty default: the docs recommend it "to ensure that you only attack sites that you mean to." Dangerous actions against anything not in your Context are refused by ZAP itself.
   - **Standard** — "does not restrict anything." Avoid on a live program.
   - **ATTACK** — new in-scope nodes are actively scanned the instant they're discovered. Powerful and dangerous; never on production.
5. **Stamp your traffic for attribution.** Many programs want to allowlist or identify tester traffic. Add an identifying header with the **Replacer** add-on (e.g. `X-Bug-Bounty: <your-handle>`) or a custom `User-Agent`, and for active scans enable *Options → Active Scan → "Inject plugin ID in header for all active scan requests"* so every attack request carries `X-ZAP-Scan-ID` — this lets the program's SOC attribute the noise to you instead of paging an incident.

Verify the lockdown: switch to Protected Mode, right-click an *out-of-scope* URL, and confirm *Attack → Active Scan* is greyed out. If it isn't, your include regex is too broad.

### Phase 1 — Passive mapping by hand (proxy + passive scanner)

Goal: map the app and harvest the free low-hanging findings without sending a single crafted request.

Point the browser's proxy at `127.0.0.1:8080`, install ZAP's root CA (*Options → Network → Server Certificates → Save/Import*) so TLS interception works, and **browse the application manually** — log in, click through every feature, submit real forms with throwaway data. The **Passive Scanner** add-on inspects every message that flows through ZAP (HTTP and WebSocket) and raises alerts without ever modifying traffic: missing/weak security headers, cookie flags (`HttpOnly`, `Secure`, `SameSite`), cache-control, information disclosure, mixed content, verbose server banners. This is safe against production and safe even on programs that forbid automated *active* scanning, because it is observation, not attack.

Work the **Sites tree** (the app's structure as ZAP sees it), the **History** tab (chronological traffic — your evidence trail), and the **Alerts** tab (passive findings, ranked by risk/confidence). Manual browsing is also the only reliable seed for the spiders: ZAP "only sees what flows through it" until you crawl.

What the passive pass hands you for free, beyond the alerts: the complete parameter inventory (every query string, form field, JSON key, and header the app uses — your fuzzing and IDOR candidate list), the authentication and session mechanism (cookie vs bearer, token format, flag hygiene), the technology fingerprint (via **Technology Detection** and response banners), and known-vulnerable client libraries (via **Retire.js**). None of it costs a crafted request, so do the whole walk-through before you touch a spider or a scanner — a thorough manual browse often makes the active scan nearly redundant for the findings that actually pay.

### Phase 2 — Crawl: Spider, AJAX Spider, Client Spider

Goal: expand the Sites tree beyond what you clicked, so later phases have a complete target map.

- **Traditional Spider** (`spider` add-on) — fast, recursive link extraction. It parses HTML tags, GET/POST form fields, and (optionally) HTML comments, but it **does not execute JavaScript**, so it misses SPA routes. Key options (*Options → Spider*): *Maximum depth to crawl* (0 = unlimited), *Number of threads used*, *Maximum duration* in minutes (0 = until exhausted), *Process forms* / *POST forms*, and *Parse robots.txt / sitemap.xml*. Two traps: parsing `robots.txt` means ZAP **harvests the disallowed paths as seeds — it does not obey them**, so a careless crawl can hit paths the program considers out of scope (exclude them in the Context first); and *Process/POST forms* means the spider **submits forms**, which can create records, send emails, or trigger workflows. Turn on **Logout Avoidance** so the crawl doesn't log itself out.
- **AJAX Spider** (`spiderAjax`) — integrates the **Crawljax** crawler driving a real browser (default *Firefox Headless*) to reach JS-rendered state, clicking elements and firing events. Slower and heavier: defaults are *Maximum Crawl Depth 10*, *Maximum Crawl States 0* (unlimited), *Maximum Duration 60 minutes*, one browser window per CPU core. Its docs now say **"the AJAX spider is no longer the recommended option for crawling modern web apps, we now recommend that you use the Client Spider instead"** — and that you should "also use the native Spider as well for complete coverage." Because it *clicks things*, it can click "Delete" or "Send": keep *Logout Avoidance* on and the destructive URLs excluded.
- **Client Spider** (`spiderClient`, the AF job `spiderClient`) — the current recommended browser-based crawler for JS-heavy apps; prefer it over the AJAX Spider on modern SPAs.

### Phase 3 — Authenticated scanning (Context auth + session management)

Goal: get ZAP to stay logged in so the spider and scanner reach protected functionality — this is where the real attack surface lives.

Each Context carries an **Authentication Method**, an **Authentication Verification Strategy**, and one or more **Users** (who hold the credentials), plus a **Session Management Method**. The core authentication methods:

| Method | When to use | Needs |
|---|---|---|
| **Manual Authentication** | quick/one-off; you log in through the proxied browser | nothing; no re-auth after logout |
| **Form-Based Authentication** | classic HTML login POST/GET | flag the login request (*Flag as… Form-Based Auth Login Request*); CSRF tokens handled if the token name is set in *Options → Anti-CSRF* |
| **JSON-Based Authentication** | JSON login API (`{"user":...,"pass":...}`) | login URL + JSON body template |
| **HTTP/NTLM Authentication** | Basic, Digest, or NTLM | the three schemes are supported directly |
| **Script-Based Authentication** | anything bespoke (multi-step, nonce, SSO) | an authentication script selected for the Context (install the **Scripts Console** add-on) |

For modern SPAs, OAuth2/OIDC, and JWT apps, install the **Authentication Helper** (`authhelper`) add-on and use **Browser Based Authentication**: it drives a real browser to log in, "automatically attempts to find suitable fields for the username and for the password" (including multi-page logins and TOTP — default period 30, 6 digits, SHA1), and ships **Auto-Detect Authentication / Auto-Detect Session Management**, an **Authentication Tester**, and a JSON **Authentication Report**. Its Automation Framework form is simply `method: "browser"` with `verification: {method: autodetect}` and `sessionManagement: {method: autodetect}` (defaults: `loginPageWait` 5 s, `stepDelay` 0, `browserId` `firefox-headless`).

When the auto-detect can't find the fields (custom components, shadow DOM, a cookie banner in the way), define explicit **manual steps** with CSS/XPath selectors: `USERNAME` (fill username), `PASSWORD` (fill password), `TOTP_FIELD` (fill the one-time code), `CUSTOM_FIELD`, `CLICK` (click an element), `AUTO_STEPS` (hand back to auto-detection), `RETURN`/`ESCAPE` (send keys), and `WAIT` (pause; step timeout default 1000 ms). Note HTTP Basic auth via the browser method is Firefox-only (a Selenium restriction), and session identification keys off the first post-login response carrying an `Authorization` header or a JSON `AccessToken`/`token`.

**Session management** is separate from authentication:
- **Cookie-Based Session Management** — session tracked via cookies (imported from the HTTP Sessions extension). The default for cookie apps.
- **HTTP Authentication Session Management** — the session rides in the `Authorization` header.
- **Header Based Session Management** (from `authhelper`) / **Script-Based Session Management** — for bearer/JWT APIs where the token is a header value, not a cookie. Browser-based auth auto-detects an `Authorization` header or a JSON `AccessToken`/`token` in the first post-login response.

A concrete form-based setup, start to finish: browse to the login page through the proxy and submit real credentials once so ZAP captures the POST; in **History**, right-click that request → *Flag as… → Form-Based Auth Login Request* (ZAP fills the login URL and body, and you mark the `username`/`password` parameters); if the form carries an anti-CSRF token, register its field name in *Options → Anti-CSRF* so ZAP refreshes it per attempt; add a **User** to the Context with the real credentials; set the verification indicator (below); then flip on **Forced User Mode** and browse — every request now carries that user's authenticated session, re-logging-in automatically when it expires. For a JWT/bearer API, use **JSON-Based Authentication** for the login call and **Header Based Session Management** so ZAP replays the `Authorization: Bearer …` token instead of a cookie.

Tell ZAP how to know it's still logged in: set a **Logged In Indicator** and/or **Logged Out Indicator** regex (*Session Properties → Authentication*), and pick a **Verification Strategy** — *Check every Response* (full-page apps), *Check every Request* (client-side state like a JWT), *Check every Request or Response*, or *Poll the Specified URL* at an interval. Without at least one indicator the strategy won't run. For hands-off re-login during scans, enable **Forced User Mode** — but note it is "just intended for manual testing and should not be used in automation." For authorization testing (Phase 5) define **two users** in the Context now: a victim and an attacker account.

### Phase 4 — Active scanning (only with program permission)

Goal: automated vulnerability probing — but this is the attack phase, so it runs **only** when the program's rules of engagement allow automated scanning, and always throttled.

**Build a Scan Policy** (*Analyse → Scan Policy Manager*) rather than running the default. A policy decides which rule families run and how aggressively, via two per-rule knobs:
- **Threshold** — "how likely ZAP is to report potential vulnerabilities." *Off* (rule disabled), *Low* (more findings, more false positives), *Medium*, *High* (fewer findings, may miss real ones). Default is *Medium*.
- **Strength** — "the number of attacks that ZAP will perform." *Low* (fewer requests, faster, may miss issues), *Medium*, *High* (more requests, slower, finds more), *Insane* ("should typically only be used for small parts of an application"). Default is *Medium*.

**For a live/production target, tune down:** set Strength to **Low**, disable rules that are noisy or destructive against prod (and anything irrelevant to the stack), and throttle hard. The modern throttle is *Options → Network → Rate Limit* (the old *Options → Active Scan → "Delay When Scanning (In Milliseconds)"* is deprecated in its favour). Cap concurrency with *"Number of Hosts Scanned Concurrently"* and *"Concurrent Scanning Threads per Host"*, and bound runtime with *"Maximum Rule Duration"* / *"Maximum Scan Duration"*. Enable *"Handle anti-CSRF tokens"* so token-protected forms are still reachable, and *"Inject plugin ID in header"* (`X-ZAP-Scan-ID`) for attribution.

Rule families come from add-ons: **Active scanner rules** (release — includes SQL Injection, Reflected/Persistent XSS, Remote OS Command Injection, Path Traversal, XXE, Cloud Metadata Attack, Log4Shell, Spring4Shell), **Active scanner rules (beta)** (CORS Header, CSRF Token, SSRF, Session Fixation, NoSQL Injection, Source Code Disclosure, Username Enumeration, Text4Shell), and **(alpha)** for the newest. Launch via *Attack → Active Scan* on the in-scope Context, and watch the *Active Scan* tab progress.

### Phase 5 — Targeted manual testing and authorization matrix

Goal: the payouts. ZAP's automated scanner finds the commodity bugs everyone's tool finds; the money is ZAP-as-workbench for logic and authorization flaws the scanner can't reason about.

- **Requester** / **Request Editor** — hand-edit and resend any captured request; the core of manual IDOR and parameter-tampering testing.
- **Fuzzer** (right-click a request → *Attack → Fuzz…*, highlight the injection point) — drive payloads from **FuzzDB Files**, **Custom Payloads**, or a script. The low-volume discipline applies: small payload sets, one location at a time.
- **Access Control Testing** (`accessControl`, alpha) — the authorization matrix built in. Define Users and per-node **Access Rules** (*Allowed / Denied / Unknown*, inherited down the URL tree), and ZAP **requests every URL as every user**, flagging violations as *Access Control Issue - Improper Authentication* (10101) and *Improper Authorization* (10102). This is your BFLA (function-level authorization) engine; for object-level IDOR/BOLA you still swap IDs by hand in the Requester, because ZAP tests URL-vs-user, not object-ownership. (This scan is blocked in Safe mode and in Protected mode when the Context isn't in scope — by design.)
- **GraphQL Support** (`graphql`) and **OpenAPI/SOAP Support** — import the schema to populate the Sites tree and feed the scanner; GraphQL resolvers rarely inherit the REST auth you already tested.
- **Retire.js** and **Technology Detection** — known-vulnerable JS libraries and stack fingerprinting, passively, as you browse.
- **Token Generation and Analysis** — statistical randomness analysis of session tokens / anti-CSRF tokens / password-reset tokens: a predictable token is a high-severity finding ZAP can actually quantify for you.

### Phase 6 — Validate, de-dupe, and export evidence

Goal: never submit a raw ZAP alert. Every scanner finding is a **candidate**: reproduce it by hand in the Requester, confirm real impact, and discard the false positives (scanners over-report headers, CSP nuances, and reflected-value "XSS" that doesn't execute). Then export (next sections).

## Where the money is with ZAP

ZAP's automated alerts cluster at the low-value, high-duplicate end — every hunter runs the same passive rules against the same program. The payouts come from using ZAP as a **proxy + fuzzer + authorization matrix** to find logic you reasoned about, not rules the tool shipped.

| Priority | Finding class | ZAP feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | IDOR / BOLA (object ownership) | Requester + two Users, manual ID swap; Compare sessions | High–Critical | Low |
| 2 | Broken function-level authz (BFLA) | **Access Control Testing** (per-user access rules, 10101/10102) | High | Low |
| 3 | SQL / command injection, XXE, SSTI | Active scanner rules (release) at tuned Strength; **Advanced SQLInjection Scanner** (`sqliplugin`, sqlmap-derived) | High–Critical | Medium |
| 4 | SSRF | Active scanner rules (beta) SSRF rule; manual fuzz of URL params with a collaborator canary | High | Medium |
| 5 | Weak/predictable tokens (session, reset, CSRF) | **Token Generation and Analysis** | Medium–High | Low |
| 6 | Reflected / stored / DOM XSS | Active scan XSS rules + manual Fuzzer confirmation; **DOM XSS** active rule | Medium–High | High |
| 7 | Known-vulnerable JS / components | **Retire.js**, **Technology Detection** | Low–Medium | High |
| 8 | CORS misconfiguration | Passive scan + beta "CORS Header" rule | Medium | High |
| 9 | CSRF on state-changing routes | beta "CSRF Token" rule + manual replay | Medium | Medium |
| 10 | Missing security headers, cookie flags, info leak | Passive Scanner | Low–Info | Very high |

Priorities 1–2 are manual work ZAP *hosts* rather than automates — that is exactly why they pay and rarely dupe. Priorities 6–10 are what a `zap-baseline`/`zap-full-scan` finds on autopilot, so expect "Informative/Duplicate" unless you prove real impact.

## Marketplace add-ons worth installing

A stock ZAP ships thin; the Marketplace (*Manage Add-ons → Marketplace*, or `-addoninstall <id>` on the daemon) is where the pentest capability lives. Install these before your first real engagement — the fastest path is the **Collection: Pentester Pack** (`packpentester`), "a collection of add-ons ideal for pentesters," plus the **Scan Rules Pack** (`packscanrules`, bundles release/beta/alpha scan rules). The individual high-value ones:

| Add-on (marketplace name) | ID | Status | Why install it |
|---|---|---|---|
| Active scanner rules (beta) | `ascanrulesBeta` | beta | SSRF, CORS, CSRF Token, NoSQL, Session Fixation, Source Code Disclosure, Username Enumeration — the next tier beyond the release pack |
| Active scanner rules (alpha) | `ascanrulesAlpha` | alpha | newest/experimental active rules |
| Advanced SQLInjection Scanner | `sqliplugin` | beta | "an advanced active injection bundle for SQLi (derived by SQLMap)" — deeper than the built-in SQLi rule |
| Access Control Testing | `accessControl` | alpha | the per-user authorization matrix (BFLA engine); alerts 10101/10102 |
| Authentication Helper | `authhelper` | beta | browser-based auth, OAuth2, auto-detect auth/session, TOTP — the modern login path for SPAs |
| GraphQL Support | `graphql` | alpha | "inspect and attack GraphQL endpoints" |
| OpenAPI Support / SOAP Support | `openapi` / `soap` | beta | import a spec to populate the Sites tree and feed the scanner |
| Retire.js | `retire` | release | vulnerable/outdated JS library detection, passively |
| Technology Detection | `wappalyzer` | release | fingerprint the stack (no add-on is literally named "Wappalyzer") |
| Token Generation and Analysis | `tokengen` | beta | statistical randomness analysis of session/CSRF/reset tokens |
| Custom Payloads | `custompayloads` | release | "add, edit or remove payloads used by active scanners" and the fuzzer |
| FuzzDB Files / FuzzDB Offensive | `fuzzdb` / `fuzzdboffensive` | release | fuzzing payload libraries (the Offensive set can trip antivirus) |
| Requester | `requester` | beta | manually edit and resend messages — the IDOR workbench |
| Replacer | `replacer` | release | rewrite strings in requests/responses — attribution headers, token swaps |
| Reveal | `reveal` | release | un-hide hidden form fields and re-enable disabled inputs |
| Forced Browse + Directory List | `bruteforce` + `directorylistv2_3` | beta/release | content discovery (OWASP DirBuster code + wordlists) |
| Scripts Console / Community Scripts | `scripts` / `communityScripts` | release/alpha | required for Script-Based auth/session; community-written checks |
| Automation Framework | `automation` | beta | YAML-driven, reproducible scan plans |
| Import/Export | `exim` | beta | HAR import/export, message/URL export for evidence |
| Report Generation | `reports` | release | the official report templates (HTML/JSON/MD/XML/SARIF/PDF) |
| Alert Filters | `alertFilters` | release | automate re-scoring/suppressing alerts — kill false positives cleanly |

### Scan-policy rule families and prod tuning

When you build a Scan Policy for a live program, enable rule families deliberately rather than "all on." The grounded families and representative alert IDs:

| Family | Representative rules (alert ID) | Prod note |
|---|---|---|
| Release active rules (`ascanrules`) | SQL Injection, Reflected XSS, Persistent XSS, Remote OS Command Injection (+ time-based), Path Traversal, XXE, Code Injection, Cloud Metadata Attack, `.env` Information Leak (40034), `.htaccess` Information Leak (40032), Buffer Overflow (30001), Log4Shell, Spring4Shell, Hidden File Finder | keep SQLi/XSS/RCE at Strength Low on prod; `.env`/`.htaccess`/Hidden File rules are cheap and low-risk |
| Beta active rules (`ascanrulesBeta`) | SSRF, CORS Header (40040), CSRF Token, Backup File Disclosure (10095), Cross-Domain Misconfiguration (20016), Session Fixation, NoSQL Injection (MongoDB), Source Code Disclosure (SVN/Git), Username Enumeration, Text4Shell (CVE-2022-42889) | SSRF/CSRF are high-value; disable Username Enumeration on auth endpoints you've been asked not to hammer |
| DOM XSS (`domxss`) | DOM-based XSS | browser-driven, slow — scope to pages with client-side sinks |
| Injection bundle (`sqliplugin`) | sqlmap-derived SQLi | run only on confirmed-injectable parameters; high request volume |

Rules you usually **disable on production**: anything time-based (they deliberately stall the server), Buffer Overflow, and the heavy injection bundle unless you've already localized the vulnerable parameter — they generate the most traffic for the least marginal finding.

## Command recipes

All examples use `example.com` / `*.example.com`. Replace with your authorized in-scope target. Passive-first is the rule: the baseline scan never attacks; the full scan does and is for systems you own or are explicitly cleared to actively scan.

```
# Passive-only CI gate — safe even against production. Spiders ~1 min, waits for
# passive scan, prints PASS/WARN/FAIL/INFO per rule, exits non-zero on a FAIL.
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py -t https://www.example.com
```
`-m 1` is the default spider minute; `-j` adds the modern browser spider "in addition to the traditional one (default: Ajax spider)" and `--client-spider` switches that to the Client Spider; `-a` adds alpha passive rules; `-D <secs>` waits longer for passive scanning to settle on a big app.

```
# Generate a tunable rule config (every rule as WARN), edit to IGNORE/FAIL/INFO,
# then gate the pipeline on it and emit an HTML artifact.
docker run -v $(pwd):/zap/wrk:rw -t ghcr.io/zaproxy/zaproxy:stable \
  zap-baseline.py -t https://www.example.com -g gen.conf
# ...edit gen.conf -> baseline.conf (lines like: 10015  IGNORE  (Cache-control))...
docker run -v $(pwd):/zap/wrk:rw -t ghcr.io/zaproxy/zaproxy:stable \
  zap-baseline.py -t https://www.example.com -c baseline.conf -r report.html -I
```
`-c` applies the config; `-r/-w/-x/-J` write HTML/Markdown/XML/JSON; `-I` means "do not return failure on warning" (keep CI green on WARN, red on FAIL). `OUTOFSCOPE` lines in the config (`<rule-id>  OUTOFSCOPE  <url-regex>`) suppress findings per URL.

```
# Authenticated baseline against an exported Context + User (set up in the GUI first).
docker run -v $(pwd):/zap/wrk:rw -t ghcr.io/zaproxy/zaproxy:stable \
  zap-baseline.py -t https://www.example.com -n example.context -U victim -r auth-report.html
```
`-n` loads the `.context` (scope + auth + session); `-U` picks a user defined inside it (post-2.9.0). Everything the Context excludes stays untouched.

```
# FULL scan — ACTIVE attacks. Owned / explicitly-authorized targets ONLY.
# Default spider has no time limit here; the active scan can run long.
docker run -v $(pwd):/zap/wrk:rw -t ghcr.io/zaproxy/zaproxy:stable \
  zap-full-scan.py -t https://staging.example.com -c policy.conf -r full-report.html
```
Here `-a` means "include the alpha active and passive scan rules." Throttle via a Context/Automation plan or the daemon's `Network → Rate Limit` — the full-scan wrapper itself exposes no rate flag.

```
# API scan tuned to an API definition (active, but API-shaped — skips XSS hunting).
docker run -v $(pwd):/zap/wrk:rw -t ghcr.io/zaproxy/zaproxy:stable \
  zap-api-scan.py -t https://www.example.com/openapi.json -f openapi -r api-report.html
# -f openapi|soap|graphql ; --schema <graphql schema> ; -O <host to override in spec>
# -S  = safe mode: skip the active scan, do a baseline instead (use on prod APIs)
```

```
# Headless daemon with an API key, then drive it over the REST API.
zaproxy -daemon -host 127.0.0.1 -port 8080 -config api.key=change-me-9203935709
# Spider, then read alerts (format /JSON/ or /OTHER/ ; component/operation):
curl 'http://127.0.0.1:8080/JSON/spider/action/scan/?apikey=change-me-9203935709&url=https://www.example.com&contextName=example'
curl 'http://127.0.0.1:8080/JSON/spider/view/status/?apikey=change-me-9203935709&scanId=0'
curl 'http://127.0.0.1:8080/JSON/ascan/action/scan/?apikey=change-me-9203935709&url=https://www.example.com&scanPolicyName=prod-low'
curl 'http://127.0.0.1:8080/JSON/core/view/alerts/?apikey=change-me-9203935709&baseurl=https://www.example.com'
```
By default ZAP "will close the connection without a response if an API request is not from an allowed address or the API key is wrong." Pass the key via `apikey=` (query) or the `X-ZAP-API-Key` header.

```yaml
# The modern, recommended path: an Automation Framework plan (zap.yaml), one file,
# reproducible, with per-job scope and an explicit exit gate. Run headless:
#   zaproxy -cmd -autorun /zap/wrk/zap.yaml
env:
  contexts:
    - name: example
      urls: [ "https://www.example.com" ]
      includePaths: [ "https://www\\.example\\.com/.*" ]
      excludePaths: [ "https://www\\.example\\.com/(logout|account/delete).*" ]
jobs:
  - type: passiveScan-config          # configure passive rules
  - type: spider                      # traditional crawl
    parameters: { maxDuration: 5 }
  - type: spiderAjax                  # browser crawl for the SPA parts (or spiderClient)
    parameters: { maxDuration: 10, browserId: firefox-headless }
  - type: passiveScan-wait
  - type: activeScan                  # ATTACK — authorized targets only
    parameters: { policy: "prod-low" }
  - type: report
    parameters: { template: "traditional-html-plus", reportFile: "zap-report" }
  - type: exitStatus
    parameters: { errorLevel: "High" } # fail the pipeline on a High-risk alert
```
Job types available include `spider`, `spiderAjax`, `spiderClient`, `activeScan`/`activeScan-config`/`activeScan-policy`, `passiveScan-config`/`passiveScan-wait`, `openapi`, `graphql`, `soap`, `import`, `postman`, `requestor`, `replacer`, `alertFilter`, `report`, and `exitStatus`.

### Key tuning options and their defaults

The settings that matter for a throttled, in-scope, authenticated run (GUI locations shown; the same keys exist in AF job parameters):

| Area | Option | Default | Bug-bounty setting |
|---|---|---|---|
| Spider | Maximum depth to crawl | 0 (unlimited) | bound it (e.g. 5) on large apps |
| Spider | Maximum duration (mins) | 0 (until exhausted) | cap it so a crawl can't run away |
| Spider | Process forms / POST forms | on | off near destructive flows; use a throwaway account |
| Spider | Parse robots.txt / sitemap.xml | per config | remember: harvests paths, does **not** obey them |
| Spider | Logout Avoidance | false | **true** |
| AJAX Spider | Browser | Firefox Headless | keep headless |
| AJAX Spider | Number of Browser Windows | Num cores | lower it to reduce load |
| AJAX Spider | Maximum Crawl Depth | 10 | fine; lower for quick passes |
| AJAX Spider | Maximum Duration (mins) | 60 | lower on prod |
| AJAX Spider | Scope Check | Strict | keep **Strict** |
| Active Scan | Threshold (per rule) | Medium | Medium; High to cut false positives |
| Active Scan | Strength (per rule) | Medium | **Low** on production |
| Active Scan | Concurrent Scanning Threads per Host | tool default | lower to throttle |
| Active Scan | Number of Hosts Scanned Concurrently | tool default | 1 on a single target |
| Active Scan | Maximum Scan Duration (mins) | 0 (unlimited) | set a hard cap |
| Active Scan | Inject plugin ID in header | off | **on** (`X-ZAP-Scan-ID`) for attribution |
| Active Scan | Handle anti-CSRF tokens | — | on for token-protected forms |
| Network | Rate Limit | off | **on** — the supported throttle (replaces the deprecated scan delay) |
| Browser auth | loginPageWait / stepDelay | 5 s / 0 | raise stepDelay for slow SPAs |

```
# Generate a report and export the PoC traffic over the API (daemon running).
K=change-me-9203935709
# Report via the reports component (template id = traditional-html-plus):
curl "http://127.0.0.1:8080/JSON/reports/action/generate/?apikey=$K&title=example&template=traditional-html-plus&reportDir=/zap/wrk&reportFileName=zap-report.html"
# Export just the messages for one base URL as HAR (evidence, not the whole session):
curl "http://127.0.0.1:8080/OTHER/exim/other/exportHar/?apikey=$K&baseurl=https://www.example.com&start=0&count=50" -o poc.har
# Or export specific message IDs you already identified as the PoC:
curl "http://127.0.0.1:8080/OTHER/exim/other/exportHarById/?apikey=$K&ids=1138,1139" -o idor-poc.har
```
The HAR endpoints are `exim.exportHar` / `exim.exportHarById` (and the `core.messagesHar` family); the report component also exposes `/JSON/reports/view/templates/` to list installed template IDs. Keep the exported HAR to the handful of messages that prove the bug.

```
# Export the Context you built in the GUI, to reuse headless / in CI:
#   Session Properties -> Contexts -> (right-click context) -> Export Context...
# then feed it to any packaged scan with -n, or to an AF plan's env.
```

## Pitfalls, noise and scope discipline

How hunters get reports rejected — or get banned from a program — with ZAP:

- **Active-scanning without authorization.** The single fastest way to a ban. The active scanner *is an attack*; its docs say not to run it on apps you don't own. Many programs forbid automated scanners entirely or require an explicit opt-in. Read the rules of engagement first; if automated scanning isn't permitted, stay in **Safe Mode** and work the proxy + passive scanner + manual Requester only.
- **Scope leak from a sloppy include regex.** `.*example\.com.*` matches `evil-example.com`, `example.com.attacker.net`, and any URL with the string in a parameter. Anchor every include (`^https://app\.example\.com/`), remember the regex must match the **whole** URL, and verify in Protected Mode that out-of-scope nodes can't be attacked. One request to a neighbouring domain you didn't mean to touch is an out-of-scope violation.
- **The spider obeying nothing.** `Parse robots.txt` harvests Disallowed paths as crawl seeds — it does **not** honour them. If the program treats those paths as out of scope, exclude them in the Context before crawling.
- **Destructive crawling.** Both spiders *submit forms*, and the AJAX/Client spiders *click elements* — they will happily POST "delete account", "cancel subscription", "send invite", or fire outbound emails. Set **Logout Avoidance**, exclude destructive endpoints, and crawl with a throwaway account.
- **DoS through volume or Strength.** *Insane* strength, high thread/host concurrency, and an uncapped scan duration can exhaust a production target — that's a program violation, not a finding. Default to Strength **Low** on prod, cap *Concurrent Scanning Threads per Host*, set *Maximum Scan Duration*, and throttle via *Network → Rate Limit*. Keep manual fuzzing to small payload sets, one injection point at a time.
- **Submitting raw scanner alerts.** Passive findings (missing headers, cookie flags, cache-control) are near-certain duplicates and usually *Informative* — programs reject them in bulk. Active-scan alerts are *candidates*: reproduce every one by hand before reporting, and discard the reflected-but-not-executing "XSS" and the context-dependent CSP nits. Tune false positives with **Alert Filters** rather than hand-editing reports.
- **Unattributed noise.** A scan that looks like an attack with no identifying marker gets you blocked by the WAF and paged by the SOC. Stamp traffic (`X-Bug-Bounty`/custom `User-Agent` via Replacer) and enable `X-ZAP-Scan-ID` injection.
- **Trusting the AJAX Spider on modern apps.** It's explicitly "no longer the recommended option" — prefer the **Client Spider**, and run the traditional Spider alongside it for coverage.
- **Silent auth failure during a scan.** The most common wasted scan is one that ran entirely logged *out* because the Logged In/Out Indicator regex was wrong, so every "finding" is just the login wall. Confirm authentication *before* scanning: with the indicator set, use the Authentication Helper's **Authentication Tester**, or watch the History tab under Forced User Mode for authenticated 200s. If the app is a SPA or uses SSO, a static Form-Based config will usually fail — switch to **Browser Based Authentication**. A scan whose coverage is all unauthenticated pages produces confident, worthless reports.
- **Treating ZAP traffic as low-volume by default.** It isn't. The spiders are multi-threaded and the active scanner sends many requests per parameter per rule; "a quick scan" can be thousands of requests in a minute. On a bug-bounty target, cap threads/hosts, set Strength Low, bound duration, and apply *Network → Rate Limit* — then watch the request rate in the footer, not just the progress bar.

## Reporting evidence

ZAP is strong on evidence because every request/response is recorded; the trick is exporting exactly what proves the bug and nothing more.

- **The request/response pair.** From **History** or **Sites**, right-click the proving message(s) → **"Save Selected Entries as HAR (HTTP Archive File)"** (the Import/Export add-on). HAR is the clean, replayable artifact for a PoC: full request and response, headers and body. The daemon equivalents are the API `exportHar` (filter by `baseurl`, paginate with `start`/`count`) and `exportHarById` (specific message IDs) — scriptable for exactly the two or three messages your finding needs. Avoid dumping the whole session; export only the PoC messages.
- **The raw message.** For a single request, *Save Raw Message* gives a copy-pasteable HTTP exchange for the report body.
- **The alert.** Export findings with the **Report Generation** add-on (*Report → Generate Report…*): pick a template — `traditional-html-plus` or `modern` for a readable human report, `traditional-json`/`sarif-json` for machine ingestion, `traditional-md` for pasting into a tracker, `high-level-report` for a summary. The dialog's **Scope** tab limits the report to your Context/Sites; the **Filter** tab (*Include Risks*, *Include Confidences*) trims it to just the High/Medium, High-confidence alerts worth submitting. From CI the same output comes from `zap-*.py -r/-w/-x/-J` or the AF `report` job.
- **What to put in the submission:** impact-first title; the exact in-scope URL and parameter; the minimal request/response pair (HAR or raw) showing the attack and the server's response; for IDOR/BFLA, the *two* request/response pairs (victim object fetched with the attacker's session) plus the account preconditions; the ZAP alert's risk/confidence and rule ID only as corroboration, never as the whole report; and a one-line, stack-specific remediation. Redact other users' data to the minimum the proof needs, and clean up any test records you created while crawling.

## References

- OWASP ZAP — Contexts — https://www.zaproxy.org/docs/desktop/start/features/contexts/
- OWASP ZAP — Scope — https://www.zaproxy.org/docs/desktop/start/features/scope/
- OWASP ZAP — Session Context screens (Include/Exclude regex) — https://www.zaproxy.org/docs/desktop/ui/dialogs/session/contexts/
- OWASP ZAP — Modes (Safe/Protected/Standard/ATTACK) — https://www.zaproxy.org/docs/desktop/start/features/modes/
- OWASP ZAP — Passive Scanning — https://www.zaproxy.org/docs/desktop/start/features/pscan/
- OWASP ZAP — Active Scanning — https://www.zaproxy.org/docs/desktop/start/features/ascan/
- OWASP ZAP — Scan Policy dialog (Threshold/Strength) — https://www.zaproxy.org/docs/desktop/ui/dialogs/scanpolicy/
- OWASP ZAP — Options: Active Scan screen — https://www.zaproxy.org/docs/desktop/ui/dialogs/options/ascan/
- OWASP ZAP — Spider add-on + options — https://www.zaproxy.org/docs/desktop/addons/spider/ , https://www.zaproxy.org/docs/desktop/addons/spider/options/
- OWASP ZAP — AJAX Spider add-on + options — https://www.zaproxy.org/docs/desktop/addons/ajax-spider/ , https://www.zaproxy.org/docs/desktop/addons/ajax-spider/options/
- OWASP ZAP — Authentication methods — https://www.zaproxy.org/docs/desktop/start/features/authentication/ , https://www.zaproxy.org/docs/desktop/start/features/authmethods/
- OWASP ZAP — Authentication Verification Strategies — https://www.zaproxy.org/docs/desktop/start/features/authstrategies/
- OWASP ZAP — Session Management — https://www.zaproxy.org/docs/desktop/start/features/sessionmanagement/
- OWASP ZAP — Authentication Helper (browser-based auth) — https://www.zaproxy.org/docs/desktop/addons/authentication-helper/ , https://www.zaproxy.org/docs/desktop/addons/authentication-helper/browser-auth/
- OWASP ZAP — Access Control Testing — https://www.zaproxy.org/docs/desktop/addons/access-control-testing/
- OWASP ZAP — Report Generation + templates — https://www.zaproxy.org/docs/desktop/addons/report-generation/ , https://www.zaproxy.org/docs/desktop/addons/report-generation/templates/
- OWASP ZAP — Import/Export add-on (HAR) — https://www.zaproxy.org/docs/desktop/addons/import-export/
- OWASP ZAP — Fuzzer — https://www.zaproxy.org/docs/desktop/addons/fuzzer/
- OWASP ZAP — Active Scan Rules (release / beta) — https://www.zaproxy.org/docs/desktop/addons/active-scan-rules/ , https://www.zaproxy.org/docs/desktop/addons/active-scan-rules-beta/
- OWASP ZAP — Marketplace add-ons — https://www.zaproxy.org/addons/
- OWASP ZAP — Docker: baseline / full / API scans — https://www.zaproxy.org/docs/docker/baseline-scan/ , https://www.zaproxy.org/docs/docker/full-scan/ , https://www.zaproxy.org/docs/docker/api-scan/
- OWASP ZAP — Automation Framework — https://www.zaproxy.org/docs/automate/automation-framework/
- OWASP ZAP — API overview — https://www.zaproxy.org/docs/api/

*Research supplement added 2026-11-15, grounded in the OWASP ZAP official documentation at zaproxy.org (contexts, scope, modes, pscan/ascan, scan-policy, spider/ajax-spider, authentication/session-management, authentication-helper, access-control-testing, report-generation, import-export/HAR, docker baseline/full/api scans, automation-framework, and the API docs) listed above (model: claude-opus-4-8).*
