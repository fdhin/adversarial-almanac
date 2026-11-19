---
name: "wfuzz"
categories:
  - blackarch-fuzzer
version: "1157.2263cd0"
homepage: "https://github.com/xmendez/wfuzz"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-01-31"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "xmendez/wfuzz"
github-stars: 6603
github-pushed: "2026-01-21"
github-archived: false
github-issues: 117
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# wfuzz

> Modular web fuzzer that injects payloads anywhere in an HTTP request via FUZZ keywords.

## What it's for

wfuzz replaces every `FUZZ`, `FUZ2Z`, … `FUZnZ` keyword in a URL, header, cookie or POST body with values from a payload — a wordlist file, a numeric range, a previous request's results, and more — which makes it a general HTTP brute-forcer: content and virtual-host discovery, credential and parameter brute force, header/cookie fuzzing, and numeric sweeps through request-reflecting endpoints. Results are filtered by status code, response size (lines, words, chars) or a regex over the body; that filtering is where the real work lives, since it separates genuine findings from the wall of identical error pages. It is a request-mutation tool at heart — vulnerability verdicts come from your filters, not from a scanning engine (optional plugin scripts add basic passive checks).

## Typical use cases

### Discover directories and files

Brute-force a site's path space with a wordlist and drop the 404 noise.

```
wfuzz -c -w /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt --hc 404,403 http://target.example.com/FUZZ
```

Outcome: one request per wordlist entry with `FUZZ` replaced; every response except 404/403 prints as a colored row (payload, status, lines, words, chars) — the surviving rows are the paths worth visiting. `-w` is shorthand for `-z file,<wordlist>`.

### Enumerate virtual hosts through the Host header

Fuzz the Host header against a single web IP and filter on the default-response signature.

```
wfuzz -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.target.example.com" -t 100 --hh 20750 http://198.51.100.10/
```

Outcome: every request hits the same IP with a different vhost name (`-t 100` raises concurrency for the large list); `--hh <chars>` — calibrate the number from one initial request's char count — hides the wildcard default page, so only vhosts that answer differently remain on screen.

### Brute-force a POST login form

Fuzz credentials inside form data, filtering on the login-failure page size.

```
wfuzz -c -w /usr/share/seclists/Passwords/darkweb2017-top1000.txt -d "username=admin&password=FUZZ&submit=Login" --hl 18 http://target.example.com/login.php
```

Outcome: sends one POST per password with `-d` supplying the request body; responses whose line count equals the known failure page (`--hl 18`) are hidden, leaving only responses that differ — typically valid credentials.

### Fuzz with an authenticated session

Carry a session cookie while brute-forcing protected paths.

```
wfuzz -c -b "PHPSESSID=abcdef0123456789" -w /usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt --hc 404 http://target.example.com/admin/FUZZ
```

Outcome: every request carries the cookie set by `-b`, letting enumeration reach content that redirects anonymous users to a login form instead of reporting it as found.

### Fuzz a cookie or header value itself

Place `FUZZ` inside an arbitrary header to brute-force secret header values.

```
wfuzz -c -w /usr/share/seclists/Passwords/darkweb2017-top1000.txt -H "Cookie: password=FUZZ" --hw 84 http://target.example.com/
```

Outcome: the wordlist is injected into the header value; `--hw 84` hides responses whose word count matches the rejected-value page, surfacing any value the server treats differently.

### Enumerate numbers with a range payload

Sweep a numeric parameter space without a wordlist file.

```
wfuzz -c -z range,1-65535 --hc 502 --hs '"endpoint":"null"' "http://target.example.com/api/v1/fetch?host=127.0.0.1:FUZZ"
```

Outcome: `FUZZ` takes every value from 1 to 65535 from the range payload; `--hc 502` hides gateway errors and `--hs <regex>` hides bodies matching the empty-result marker, so surviving rows identify the port or ID values the endpoint actually answers for (`--ss <regex>` is the inverse — show only matching bodies).

### Discover hidden parameters

Brute-force parameter names against a URL, filtering on the application's default response size.

```
wfuzz -c -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt --hh 4228 "http://target.example.com/index.php?FUZZ=1"
```

Outcome: each candidate name is sent as a GET parameter; when an unknown parameter normally changes nothing, `--hh <default-chars>` leaves only the names that alter the response — typically validated or behavior-changing parameters.

### Combine two wordlists with FUZ2Z

Fuzz two positions in one request, for example credential pairs.

```
wfuzz -c -z file,users.txt -z file,passwords.txt --sc 200 "http://target.example.com/login?user=FUZZ&pass=FUZ2Z"
```

Outcome: the first `-z` payload feeds `FUZZ` and the second feeds `FUZ2Z`, producing one request per combination (product iteration by default); `--sc 200` keeps only responses with the success status.

### Brute-force HTTP basic authentication

Feed the password slot of basic auth from a wordlist.

```
wfuzz --basic admin:FUZZ -w /usr/share/seclists/Passwords/darkweb2017-top1000.txt http://target.example.com/admin/
```

Outcome: every request carries basic-auth credentials with a different password; a response that no longer returns 401 identifies the valid password. `--ntlm` and `--digest` accept the same `user:pass` format.

### List available payloads and encoders

Discover what wfuzz can inject before writing a command.

```
wfuzz -e payloads
```

Outcome: prints the payload plugins (`file`, `range`, `hex`, `permutation`, …) usable in `-z type,params,encoder` specs; `-e encoders`, `-e iterators`, `-e printers` and `-e scripts` list the other extension types. In a payload spec, encoders can be listed (`md5-sha1`), chained (`md5@sha1`) or picked by category (`url`).

### Save and replay a full session as a recipe

Capture a working command line, then re-run it with a single flag.

```
wfuzz -c -w dirs.txt --hc 404 --dump-recipe /tmp/wfuzz.recipe http://target.example.com/FUZZ
wfuzz --recipe /tmp/wfuzz.recipe -b 'session=abc123'
```

Outcome: `--dump-recipe` writes every option of the run — payloads, filters, headers, URL — to a file; `--recipe` re-executes that stored session verbatim, and further flags on the recipe invocation (like the `-b` cookie here) are merged on top, so an authenticated re-run needs no retyping.

### Fuzz the HTTP method

Probe which verbs an endpoint accepts.

```
wfuzz -c -z list,GET-HEAD-POST-TRACE-OPTIONS -X FUZZ http://target.example.com/
```

Outcome: `-X FUZZ` places the payload in the request-method position; the list payload cycles GET/HEAD/POST/TRACE/OPTIONS and the response table shows each verb's status and size (405 vs 200) — a quick map of what the route tolerates. A fixed value like `-X PATCH` instead forces one non-default verb for every request.

### Pair two wordlists line-by-line

Test matched credential pairs instead of the full cross product.

```
wfuzz -c -z file,users.txt -z file,passwords.txt -m zip --sc 200 "http://target.example.com/login?user=FUZZ&pass=FUZ2Z"
```

Outcome: two payloads iterate as a cartesian product by default; `-m zip` switches to element-wise iteration, walking both files in lockstep and stopping at the shorter one — the right mode when the two wordlists are matched columns of one leaked table. `wfuzz -e iterators` lists the alternatives (`product`, `zip`, `chain`).

### Route all fuzzing traffic through a proxy

Watch or modify every request in an intercepting proxy.

```
wfuzz -c -w users.txt -p 127.0.0.1:8080:HTTP http://target.example.com/FUZZ
```

Outcome: `-p host:port[:TYPE]` sends every request through the given proxy — the way to observe wfuzz inside an intercepting proxy, debug blocked requests, or relay through the same chain the browser uses. The type suffix selects `HTTP`, `SOCKS4`, or `SOCKS5`; plain HTTP is the default, so `-p 127.0.0.1:8080` alone works too.

---

## Bug bounty workflow with wfuzz

**Audience and rules of engagement.** Everything below assumes an authorized program with written permission, and that you respect its rate limits and scope. wfuzz is a request *multiplier*: every command here fans one template into hundreds or thousands of live requests, so the defaults matter. Treat `-t` (threads) and `-s` (per-request delay) as safety controls, not performance knobs — on a production target the correct posture is low concurrency with a delay, and the recursion flag (`-R`) is the one setting most likely to turn a tidy scan into an unintended flood. The payoff wfuzz gives you over a faster tool is *precision*: the richest response-filter language of any CLI fuzzer, automatic baseline calibration, and the ability to inject anywhere in the request, not just the path.

A note on tool fit before you start. wfuzz is slow — community benchmarks routinely put it under ~100 req/s where a Go fuzzer like ffuf clears several thousand on the same wordlist. It is also effectively dormant upstream (one commit in the past year; the actively maintained descendant is the `wenum` fork, see References). So reach for wfuzz when you need its filter grammar, its recipe/replay machinery, or its Burp-state payloads — and reach for ffuf when the job is raw content discovery against a large wordlist where throughput is the only variable that matters. The two are complements, not rivals; many hunters do bulk discovery in ffuf and precision follow-up in wfuzz.

### Phase 0 — Scope intake and baseline calibration

Goal: a reproducible, program-safe command skeleton and the baseline responses you will filter against. Skipping calibration is the single biggest cause of wfuzz output that is either all noise or silently empty.

First pin the rate posture for the whole engagement so you cannot forget it mid-run. The per-connection defaults live in `~/.wfuzz/wfuzz.ini` under `[connection]` (`concurrent`, `conn_delay`, `req_delay`, `retries`, `user-agent`); set a conservative `concurrent` there once, or pass `-t`/`-s` on every command.

Then calibrate. wfuzz's distinguishing feature is the **baseline request**: a `FUZZ{value}` marker fires one request first with `value` substituted, and the special filter literal `BBB` refers to that baseline's response. This lets wfuzz compute the "normal" response signature for you instead of eyeballing a char count:

```
wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/raft-small-words.txt \
  --hh=BBB -t 15 -s 0.2 "http://target.example.com/FUZZ{thisdoesnotexist12345}"
```

The baseline hits a path you know is absent, so `BBB` captures the real 404/soft-404 size; `--hh=BBB` then hides every response whose char count equals that baseline — including soft-404s that return `200 OK` with a "not found" body, which a plain `--hc 404` would miss entirely. Record the baseline's code/chars from the first output row; it is your diff justification in the report.

### Phase 1 — Content and endpoint discovery

Goal: paths, backups, and config files, with the 404 wall removed by calibration rather than guesswork.

```
wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt \
  --hc=BBB -R2 -t 15 -s 0.1 "http://target.example.com/FUZZ{nonexistent-baseline}"
```

`-R2` recurses two levels into any directory found — powerful, and the flag to respect: recursion multiplies the wordlist against every discovered directory, so request volume grows geometrically. On a live program cap it at `-R1`, keep the wordlist small, and watch the running total. For sensitive-file sweeps, swap in a backup/config list and show only hits whose body differs from the baseline:

```
wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/backup-files.txt \
  --hc=BBB,403 -t 10 -s 0.2 "http://target.example.com/FUZZ{nope-baseline}"
```

### Phase 2 — Parameter and vhost discovery

Goal: the hidden inputs that feed Phase 3/4. Parameter mining is where wfuzz's byte-level filtering earns its slowness — an unknown parameter usually changes *nothing*, so you filter on exact size against the baseline and keep only names that move the response.

```
wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
  --hh=BBB -t 15 -s 0.1 "http://target.example.com/profile?FUZZ=FUZZ{calibrate}"
```

Here the baseline value (`calibrate`) is the *parameter value*, so `BBB` is "a benign param that changes nothing"; surviving rows are names the app actually consumes. Virtual-host discovery against a single IP uses the same baseline discipline on the `Host` header:

```
wfuzz -c -z file,/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -H "Host: FUZZ{www}.target.example.com" --hh=BBB -t 20 -s 0.05 http://198.51.100.10/
```

### Phase 3 — Object enumeration and access-control testing (the money)

Goal: IDOR/BOLA and tenant crossing. wfuzz's `range` payload sweeps numeric object IDs with no wordlist, and its filters confirm access rather than just discovery. The correct method is a *two-identity* test: enumerate an object space with account A's session, then prove the objects belong to someone else.

Authenticate first. For cookie sessions use `-b`; to inherit a full authenticated request captured in Burp, feed a saved state as the payload source so headers, cookies, and body all come along:

```
# enumerate invoice IDs as account A, hide the "not yours" 403/404 signature via baseline
wfuzz -c -b "session=A_SESSION_TOKEN" -z range,1000-1200 \
  --hc=BBB -t 8 -s 0.3 "http://target.example.com/api/invoices/FUZZ{999999}"
```

A `200` on an ID you never created is the BOLA signal. To surface cross-tenant *content* rather than just status, show only bodies that contain another user's marker with `--ss` (show-when-body-matches, a regex over the response):

```
wfuzz -c -b "session=A_SESSION_TOKEN" -z range,1000-1200 \
  --ss "owner_email" -t 8 -s 0.3 "http://target.example.com/api/invoices/FUZZ"
```

Keep the volume demonstrative, not exhaustive — a 200-ID window proves the class; you do not need to pull the whole table, and pulling it is exactly the over-collection that gets reports penalized.

### Phase 4 — Injection and behavior discovery

Goal: find where input reaches a sink, using `--ss`/`--hs` (show/hide by body regex) to catch error strings a size filter would miss. Fuzz the *value*, filter on the sink's tell:

```
# reflected SQL error strings — show only responses whose body leaks a DB error
wfuzz -c -z file,/usr/share/seclists/Fuzzing/SQLi/Generic-SQLi.txt \
  --ss "SQL syntax|ORA-[0-9]|SQLSTATE" -t 8 -s 0.3 \
  "http://target.example.com/search?q=FUZZ"
```

Encoder chains transform payloads inline, which is useful for matching an endpoint's expected encoding (and, on explicitly authorized WAF-evaluation work, for testing filter bypasses). In a `-z` spec, a `-`-separated list runs each encoder as its *own pass* (`md5-sha1-none` turns 3 payloads into 9 requests), while `@` *chains* encoders within one pass (`base64@urlencode` base64-encodes then URL-encodes the same value). List what is available with `wfuzz -e encoders`, and select a whole category by name (`wfuzz -z list,a-b-c,hashes`). Note the request-count multiplication before you run a multi-encoder list against a live target.

### Phase 5 — Validate, capture, and report

Goal: turn a filtered row into a reproducible, single-request proof. Save the exact command as a recipe so the triager can replay it, and route the proof request through Burp for a clean request/response pair:

```
wfuzz -c -b "session=A_SESSION_TOKEN" -z range,1000-1200 --ss "owner_email" \
  --dump-recipe /tmp/invoice-bola.recipe -f /tmp/hits.json,json \
  -p 127.0.0.1:8080 -t 4 -s 0.5 "http://target.example.com/api/invoices/FUZZ"
```

`--dump-recipe` writes every option to a YAML file replayable with `wfuzz --recipe`; `-f file,json` saves the result rows in the `json` printer format for your notes; the proxy captures the on-wire request/response. Reduce to one ID for the final PoC — the report needs *one* request that demonstrates the bug, not the sweep that found it.

## Where the money is with wfuzz

| Finding class | wfuzz feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| IDOR / BOLA on numeric objects | `-z range,N-M` + session `-b`/burpstate, `--ss`/`BBB` filter | High–Critical | Low–Medium |
| Hidden parameters → debug/injection | `?FUZZ=x` param-name list, `--hh=BBB` | Medium–High | Medium |
| Sensitive file/backup exposure (`.env`, `.git`, `*.bak`) | backup/config wordlist, `--hc=BBB` | Medium–High | High |
| Virtual-host / internal app discovery | `-H "Host: FUZZ"`, `--hh=BBB` | Medium (scope expansion) | Medium |
| Reflected injection (SQLi/XSS/SSTI) sinks | payload list + `--ss "<error regex>"` | High | Medium |
| Open redirect / SSRF parameter abuse | fuzz redirect/url params, inspect `Location` via `--filter` | Medium–High | Medium |
| Hidden endpoints / forgotten API routes | directory/API wordlist, `-R1` recursion | Medium | High |
| Auth-token / API-key value brute | `-H`/`-b` with `FUZZ` in the secret slot | varies | **often out of scope** |

The low-dupe, high-value lane is **BOLA via `range`** and **parameter mining** — behavior-level findings a plain content-discovery scan never surfaces. Content and backup-file discovery pay less and collide constantly with other hunters (and with the program's own scanners), so lead with access-control work and treat path discovery as support.

## Command recipes

Every recipe below is tuned for a live program: low threads, a delay, baseline-calibrated filters, passive-capture where possible. Dial `-t` down and `-s` up further on any target with visible rate limiting.

```
# Calibrated directory discovery — baseline kills soft-404s a status filter misses
wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt \
  --hc=BBB -t 10 -s 0.2 "http://target.example.com/FUZZ{random-nonexistent-9f3}"
# -> rows where chars != baseline; each is a real directory, soft-404s already hidden
```

```
# Paired credential list, element-wise (not cross product) — a leaked user:pass table
wfuzz -c -z file,userpass_users.txt -z file,userpass_pwds.txt -m zip \
  --ss "Welcome|dashboard" -t 1 -s 1 \
  -d "user=FUZZ&pass=FUZ2Z" "http://target.example.com/login"
# -m zip walks both files in lockstep; -t 1 -s 1 keeps auth attempts slow and lockout-safe
```

```
# BOLA sweep with full authenticated context replayed from a Burp save
wfuzz -c -z burpstate,/tmp/authed_request.burp -z range,2000-2100 \
  --ss "account_number" -t 6 -s 0.4 FUZZ
# the burpstate payload carries A's cookies/headers; range drives the object id
```

```
# Show-only on a sink tell, routed through Burp for evidence capture
wfuzz -c -z list,'<script>-"><img src=x>-{{7*7}}' \
  --ss "49|<script>|<img src=x" -p 127.0.0.1:8080 -t 4 -s 0.3 \
  "http://target.example.com/profile?bio=FUZZ"
# list payload inlines three probes; --ss surfaces reflection/SSTI; proxy records the pair
```

```
# Ignore transient network errors instead of aborting a long authorized run
wfuzz -c -Z -z file,words.txt --hc=BBB,XXX -t 8 -s 0.2 \
  "http://target.example.com/FUZZ{baseline}"
# -Z continues past connection errors (shown as code XXX); --hc XXX hides them from output
```

```
# Replay a saved recipe, layering a fresh session cookie on top
wfuzz --recipe /tmp/invoice-bola.recipe -b "session=NEW_TOKEN"
# command-line options override recipe values; last --recipe wins when several are given
```

Low-volume checklist: `-t` low and `-s` set on every live command; `-R` capped at 1 (or omitted) unless you are watching the request counter; wordlists scoped to the task, not the 220k-line everything-list; `--ss`/`--hs`/`BBB` filters so output is signal, not a wall; `-p` through Burp on anything you intend to report.

## The filter language and result chaining

The `--hc/--hl/--hw/--hh` and `--sc/--sl/--sw/--sh` flags are shortcuts; wfuzz's real edge over faster fuzzers is the full filter expression language behind `--filter` (post-filter), `--prefilter` (mutate the request before sending), and `--slice` (filter the payload stream). `wfuzz --filter-help` prints the grammar; the parts that pay off in bounty work:

- **Boolean / comparison:** `and`, `or`, `not`; `=`, `!=`, `<`, `>`, `>=`, `<=`.
- **Text match:** `=~` (regex), `~` (case-insensitive "contains", i.e. Python `in`), `!~` (negated contains).
- **Result fields:** `code`/`c`, `lines`/`l`, `words`/`w`, `chars`/`h`, `content`, `url`, `plugins`, `history`/`r`, `md5`, `nres`, `description`.
- **Special literals:** `BBB` (the baseline response) and `XXX` (a request error).
- **Request/response introspection via `r.`:** `r.method`, `r.url`, `r.reqtime`, `r.content`, `r.headers.response.<name>`, `r.headers.request.<name>`, `r.cookies.response.<name>`, `r.params.get.<name>`, `r.params.post.<name>`, plus parsed-URL fields under `urlp.*` (`urlp.path`, `urlp.query`, `urlp.fext`, …).
- **Payload back-reference:** `FUZZ[field]` (e.g. `FUZZ[c]`) is the *payload's own* value/property, letting you compare a response to a property of the thing you sent.

Because `content`, response headers, and cookies are all addressable, the filter does triage a size-based tool cannot. Bug-bounty-shaped filters:

```
# Open redirect: keep only rows whose Location header reflects your injected host
wfuzz -c -z file,/usr/share/seclists/Fuzzing/open-redirect-payloads.txt \
  --filter "r.headers.response.Location=~'evil\\.example'" -t 6 -s 0.3 \
  "http://target.example.com/out?url=FUZZ"
```

```
# Reflection hunting: show rows where the payload comes back in the body (XSS/SSTI lead)
wfuzz -c -z list,'<x9f>-{{7*7}}' --filter "content~FUZZ or content~'49'" \
  -t 6 -s 0.3 "http://target.example.com/search?q=FUZZ"
```

```
# Auth state change: surface requests that cause the server to set a session cookie
wfuzz -c -z file,endpoints.txt --filter "r.cookies.response.session" \
  --hc=BBB -t 8 -s 0.2 "http://target.example.com/FUZZ{baseline}"
```

`--prefilter` goes the other way, rewriting the composed request before it is sent with the assignment operators `:=` (set), `=+` (prepend), `=-` (append) — useful for appending a tracking marker to every value, or normalizing a parameter across a run. `--slice` trims the *payload* stream with a value expression: `--slice "FUZZ|u()"` keeps unique payloads (`u()`/`unique()`), and the value functions (`lower()`/`l()`, `upper()`, `encode()`/`e()`, `decode()`/`d()`, `replace()`/`r()`, `startswith()`/`sw()`, `gregex()`/`gre()`) transform payloads inline without a pre-processing script.

**Result chaining — feed one run into the next.** wfuzz can save a run as a `wfuzzp` session (`--oF <file>`) and consume it as a payload later, pulling a specific field out of each stored result with `FUZZ[field]`:

```
# 1) discover directories, save the session
wfuzz -c -z file,dirs.txt --hc=BBB --oF /tmp/dirs.wfuzzp \
  -t 10 -s 0.2 "http://target.example.com/FUZZ{baseline}"
# 2) fuzz a sensitive file under every directory the first run found
wfuzz -c -z wfuzzp,/tmp/dirs.wfuzzp -z list,'.git/config-.env-backup.zip' \
  --hc=BBB -t 8 -s 0.2 "FUZZ[url]/FUZ2Z"
```

The same mechanism imports an entire Burp session as a payload source: `-z burpstate,state.burp` or `-z burplog,proxy.log` carry real authenticated requests captured in Burp into a wfuzz sweep — the cleanest way to fuzz a flow that needs CSRF tokens or multi-header auth you would otherwise have to reconstruct by hand. `--prev` prints the stored request for a given result so you can show exactly what was sent.

**Passive plugins, no extra requests.** `-A` (alias for `--script=default`) and `--script=<name|category>` run parse/scan plugins over responses you were already fetching — link/robots extraction, header analysis — and their output is filterable via the `plugins` field (`--filter "plugins~'sqli'"`). The passive-discovery plugins add intelligence to a discovery pass without adding request volume, which keeps you inside a program's rate budget while widening coverage.

**Origin pinning.** `--ip 198.51.100.20` connects to a specific address while leaving the URL's `Host` intact — the move for testing a suspected origin behind a CDN/WAF (authorized targets only), where reaching the origin IP directly with the production `Host` header can reveal unfiltered behavior the edge was hiding.

## Scriptability vs speed: the Python library and ffuf

The one decision that should precede every wfuzz run is whether wfuzz is the right tool for *this* step. The honest trade-off:

- **ffuf wins on throughput.** It is a Go tool clearing thousands of requests per second; community benchmarks put wfuzz under ~100 req/s on the same wordlist. For bulk content discovery against a large list, ffuf finishes in a fraction of the time and its auto-calibration (`-ac`) plays the same role as wfuzz's `BBB` baseline.
- **wfuzz wins on precision and programmability.** Its filter grammar (response headers, cookies, parsed-URL fields, payload back-references, value functions) is richer than ffuf's matcher/filter set, and — the decisive feature for automation — wfuzz *is a Python library*, not only a CLI. A finding that needs custom per-response logic, stateful multi-step sequences, or integration into a larger Python harness is wfuzz's lane.

The library exposes the same engine the CLI drives. The documented entry points are `wfuzz.get_payload()` / `wfuzz.get_session()` for building a run and `FuzzSession` for a full configuration object; `.fuzz()` runs it and takes the same options as keyword arguments (the docs' own example is `wfuzz.get_payload(range(100)).fuzz(hl=[97], url="http://.../listproducts.php?cat=FUZZ")`). Iterating the result yields objects carrying the same fields the filter language exposes:

```python
import wfuzz

# programmatic BOLA sweep: drive the engine, apply arbitrary Python to each result
# kwargs mirror the CLI filters: hc -> --hc. Confirm option names against your build.
for r in wfuzz.get_payload(range(1000, 1200)).fuzz(
        url="http://target.example.com/api/invoices/FUZZ",
        hc=[404, 403]):
    # r.code, r.chars, r.history.content — the filter fields, as attributes
    if r.code == 200 and b"owner_email" in r.history.content:
        print(f"[BOLA] invoice {r.description}: {r.code} {r.chars}c")
```

This is why wfuzz survives despite its speed: when the check is "for each object ID, fetch it, parse the JSON, compare a field across two sessions, and flag mismatches," a few lines of Python around the wfuzz engine beat shelling out and re-parsing text. (Note: much of the upstream library documentation still shows Python 2 syntax; the engine runs on Python 3, but confirm method signatures against your installed version — the project is dormant and the docs trail the code.) The practical division of labor on a live program: ffuf for the wide, fast discovery pass; wfuzz — CLI for filtered follow-up, library for anything that needs real logic — for the precise, authenticated, evidence-grade work that turns a candidate into a report.

## Pitfalls, noise and scope discipline

**Recursion is the flood trap.** `-R` re-applies the entire wordlist to every directory it finds, and with a medium list plus `-R3` a "quick scan" becomes tens of thousands of requests against production in minutes — the classic way to trip a WAF, exhaust a rate limit, or get an IP banned mid-engagement. Default to no recursion; when you use it, use `-R1`, a small list, and a delay.

**Credential brute force is where programs draw the line.** Password spraying and auth brute force (the `--basic FUZZ:FUZZ`, `-d "pass=FUZZ"` patterns) are explicitly *out of scope* in most bounty programs, cause real account lockouts, and generate exactly the volume that gets a researcher banned. Do them only when the rules of engagement name them as allowed, and even then keep `-t 1 -s 1`.

**Uncalibrated filters produce false everything.** Without a `BBB` baseline, `--hc 404` misses soft-404s (apps that answer `200` with a "not found" body) — so your results are drowned in noise, or, if you over-filter on a guessed char count, silently empty. Always calibrate against a known-absent request first and filter on `BBB`, not a hand-typed number.

**Speed expectations cause abandoned scans.** wfuzz is slow by design; a large wordlist at safe thread counts can run for an hour. That is not a reason to crank `-t` into the hundreds on a live target — it is a reason to scope the wordlist, or to do the bulk discovery pass in a faster tool and bring the interesting candidates back to wfuzz for filtered, authenticated follow-up.

**Over-collection on IDOR.** Once one unauthorized object returns `200`, stop. Pulling the full ID range exfiltrates real users' data, inflates your request count, and converts a clean access-control report into a data-handling problem the triager now has to worry about. Prove the class on a small window and move on.

**Encoder multiplication.** A `-`-separated encoder list multiplies your request count by the number of encoders (`md5-sha1-none` = ×3). Know the multiplier before you launch a multi-encoder spec at a live host.

## Reporting evidence

Capture, for every wfuzz finding:

- **The exact command**, ideally as a `--dump-recipe` file so the triager can replay it verbatim (`wfuzz --recipe <file>`). Include the baseline marker you calibrated against.
- **The baseline response** (code and char count) you filtered on, so the "why these rows survived" logic is explicit and reproducible — this is what makes a size-based filter defensible rather than arbitrary.
- **The surviving result rows** (payload, code, lines, words, chars), saved with `-f results.json,json` or a selected printer. Trim to the rows that matter.
- **A single clean request/response pair** for the PoC, captured by routing the proof request through an intercepting proxy with `-p 127.0.0.1:8080` (or shown with `--prev` for the stored request). The report needs one demonstrative request, not the sweep.
- **For IDOR/BOLA:** the two-identity proof — the request as account A reaching account B's object, plus a control showing the object is genuinely B's (e.g. the same object fetched with B's own session). Redact the other user's real data to the minimum the proof needs.
- **Scope/volume notes:** the thread and delay settings you used, and the fact that you capped the sweep — evidence that you tested within the program's rules.

## References

- wfuzz — official documentation (basic usage) — https://wfuzz.readthedocs.io/en/latest/user/basicusage.html
- wfuzz — advanced usage (filter language, encoders, iterators, recipes, scan mode) — https://wfuzz.readthedocs.io/en/latest/user/advanced.html
- wfuzz — documentation index and CLI usage summary — https://wfuzz.readthedocs.io/en/latest/
- wfuzz — GitHub repository (README, releases, issues) — https://github.com/xmendez/wfuzz
- wenum — maintained wfuzz fork / successor (upstream) — https://github.com/WebFuzzForge/wenum
- SecLists — wordlists referenced in the recipes above — https://github.com/danielmiessler/SecLists

*Research supplement added 2026-11-16, grounded in the official wfuzz readthedocs (basicusage/advanced/index) and the xmendez/wfuzz and WebFuzzForge/wenum repositories (model: claude-opus-4-8).*
