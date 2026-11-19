---
name: "nikto"
categories:
  - scanner
homepage: "https://cirt.net/Nikto2"
origin: "corpus"
recipe: "upstream"
packaged-by: [arch, nixpkgs]
build-system: perl
github-repo: "sullo/nikto"
github-stars: 10852
github-pushed: "2026-11-09"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-13"
github-commits-year: 179
gh-activity-checked: "2026-11-13"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
last-refresh: "2026-11-19"
license: "GPL-3.0"
---

# nikto

> Long-running open-source web server scanner: a large checks database for dangerous files, misconfigurations, and outdated components.

## What it's for

Nikto is one of the oldest and best-known web server scanners: given a target host, it probes for dangerous files and CGI scripts, default installations and default credentials, server misconfigurations, information disclosure, and outdated server software, reporting every hit with details and references. It is fast to start and famously noisy — its test traffic is easy to fingerprint, and beyond the `-evasion` request encodings it makes no attempt at stealth, so treat results as a checklist generator rather than an assessment of what an attacker could really see. Tuning flags scope scans to specific test classes, output flags write reports in text, HTML, JSON, XML, CSV, or SQL, and basic-auth targets can be scanned with supplied credentials. It tests servers, not application logic: multi-step web-app flows are out of scope.

## Typical use cases

### Scan a web server

The default full battery against a web target.

```
nikto -h http://target.example.com
```

Outcome: the complete default test set runs against the host; findings print with `+` markers (location, detail, reference) as they are found, followed by a per-test summary — expect several minutes on a large site.

### Scan a non-standard HTTPS port

Point the scanner at a TLS service that is not on 443.

```
nikto -h 198.51.100.10 -port 8443 -ssl
```

Outcome: the same battery runs over TLS on the given port (`-h host:port` is the shorthand equivalent); `-ssl` forces SSL mode regardless of the port number.

### Scope the scan to chosen test types

Cut runtime and noise by selecting test classes.

```
nikto -h target.example.com -Tuning 1,2,3
```

Outcome: only the selected test types run — 1 interesting files, 2 misconfiguration/default files, 3 information disclosure (4 injection/XSS, 8 command execution, 9 SQL injection, 0 file upload, and so on); prefix the list with `x` to invert it to "everything except".

### Save a report for the engagement

Produce a file artifact alongside terminal output.

```
nikto -h target.example.com -output nikto-report.html -Format htm
```

Outcome: findings are also written to the named file in the chosen format (`csv`, `json`, `htm`, `sql`, `txt`, `xml`); without `-Format`, the format is inferred from the output filename's extension.

### Obfuscate requests to slip past naive signatures

Make the probes harder to pattern-match.

```
nikto -h target.example.com -evasion 1
```

Outcome: requests use random non-UTF8 URI encoding; techniques can be combined by listing numbers (`-evasion 1,4,7` — long random prefix, URL case changes, and more, plus `A`/`B` spacer tricks).

### Enumerate users via Apache home directories

Guess valid usernames as recon for later attacks.

```
nikto -h target.example.com -mutate 3
```

Outcome: the mutation plugin requests `/~user` paths for guessed usernames and reports hits; other mutations guess password filenames (`2`), brute-force subdomains (`5`), or guess directories from a dictionary supplied via `-mutate-options`.

## Bug bounty workflow with nikto

Nikto is not a vulnerability finder — it is a **lead generator**. It fires a large static database of signatures (dangerous files, default installs, outdated banners, known-bad paths) at one host and prints every match. On a bug bounty program that makes it useful for exactly one thing: turning a single in-scope host into a short list of things to look at by hand. Everything it reports is a *claim*, not a confirmed finding, and the signal-to-noise ratio is poor by design — the whole workflow below exists to extract the few real leads and discard the rest before you ever write a report.

The governing constraint: **one authorized host, low volume, results treated as a to-do list.** Nikto's traffic is trivially fingerprinted (static paths, a known default User-Agent, a recognizable request cadence), so mass-scanning a program's scope is the fastest way to get your IP banned and your account flagged — see the scope-discipline section. Use it on a host you have explicitly in scope, tuned down to the categories that actually pay, and verify every hit manually.

### Phase 0 — Authorization and scope check

Before the first request, confirm the host is in scope and that automated scanning is permitted. Many programs cap request rates or forbid automated scanners outright; nikto's volume (hundreds to thousands of requests per host) violates a "no automated scanning / manual testing only" clause instantly. Read the rules of engagement, note any rate cap, and pick **one** host. Never point nikto at a wildcard scope or a list of hosts you have not individually confirmed.

```
# Confirm the service answers and capture a baseline before scanning
curl -sI https://target.example.com/
```

Record the `Server` header, any framework cookies, and the default 404 behavior — you will need them to sanity-check nikto's banner-based findings later.

### Phase 1 — A scoped, low-noise first pass

Do not run the default full battery on a bounty target as your opener. The default set includes denial-of-service probes (category `6`) and thousands of signature requests you do not need. Scope the run to the high-value, low-risk categories and pace it:

```
nikto -h https://target.example.com \
  -Tuning 123b \
  -Pause 1 \
  -timeout 10 \
  -maxtime 30m \
  -nointeractive \
  -output nikto-target-pass1.xml -Format xml
```

- `-Tuning 123b` — interesting files (`1`), misconfiguration/default files (`2`), information disclosure (`3`), software identification (`b`). This deliberately **excludes** the noisy and dangerous categories (`4` injection, `6` DoS, `8` command execution, `9` SQLi) on the first pass; nikto's injection/SQLi signatures are high-false-positive and better confirmed with a dedicated tool.
- `-Pause 1` — one second between tests, the simplest rate throttle. Raise it on programs with strict caps.
- `-maxtime 30m` — a hard per-host time budget (accepts `1h`/`60m`/`3600s`) so a slow target cannot run for hours.
- `-nointeractive` — disables the runtime keypress features so the run is script-safe and unattended.
- `-output … -Format xml` — a structured artifact you can diff and attach. (`-Format` also accepts `csv`, `json`, `htm`, `txt`, `sql`.)

### Phase 2 — Triage the output into a lead list

Read the report as a queue of "go look at this," ranked by what nikto claims it found. Collapse the categories that are known noise (missing security headers, directory-indexing on static asset dirs, generic "this might be interesting" hits on common paths) and pull out the genuinely interesting leads: an exposed `.git`/`.env`/backup path, an admin console, a default install, a dangerous HTTP method, an outdated-software banner that maps to a real CVE. Nothing here is reportable yet.

A realistic output block, triaged line by line:

```
+ Server: Apache/2.4.29 (Ubuntu)
+ /: The X-Content-Type-Options header is not set.              [noise  — Info, mass-reported]
+ /: The anti-clickjacking X-Frame-Options header is not present.[noise — Info, mass-reported]
+ Apache/2.4.29 appears to be outdated (current is at least …).  [LEAD  — verify → CVE pivot]
+ /icons/README: Apache default file found.                      [noise — default file, Info]
+ /css/: Directory indexing found.                               [maybe — only if it lists secrets]
+ /.env: Uncommon header 'x-amz-...' / file present.             [LEAD  — likely live secrets, High]
+ /.git/HEAD: Git repository found.                              [LEAD  — source/secret disclosure]
+ /admin/login.php: Admin login page.                            [LEAD  — default creds / BFLA pivot]
+ OPTIONS: Allowed HTTP Methods: GET, POST, PUT, OPTIONS         [LEAD  — PUT → upload test]
+ /backup.zip: Potentially interesting backup/cert file.         [LEAD  — verify contents]
```

Four or five genuine leads out of eleven lines is a *good* ratio for nikto; the two header lines and the `/icons/README` default file are the noise you drop. The `/css/` indexing is conditional — worth a 30-second look, not a report by itself. Everything tagged LEAD goes to Phase 3.

### Phase 3 — Manual verification (the part that matters)

Every lead gets re-requested by hand and confirmed before it becomes a finding. Nikto flags paths by signature match, not by reading what came back in context — so a "200 OK" on a signature path can be a soft-404, a login wall, a WAF interstitial, or a genuinely exposed file. Open each lead in a browser or with `curl -i`, read the actual response, and decide. Banner-based "outdated software" findings in particular are **never** reportable on the banner alone: confirm the version maps to an exploitable CVE and demonstrate the condition.

### Phase 4 — Report only verified, impactful leads

Write up the handful of hits that survived Phase 3, with the manual PoC — not nikto's output line. Nikto's role in the report is provenance ("discovered via nikto, confirmed manually"), never the evidence itself. See the reporting section.

## Where the money is with nikto

Nikto rarely hands you a Critical directly; its value is surfacing exposed-file and misconfiguration leads fast. This table ranks what it finds by the realistic bounty payoff *after manual confirmation*, with the tuning category that reaches each class and the dupe risk you are fighting.

| Priority | Finding class | Reached by | Typical severity | Dupe / noise risk |
|---|---|---|---|---|
| 1 | Exposed secrets / config / backup files (`.env`, `.git/config`, `*.bak`, DB dumps) | `-Tuning 1` (interesting files), `-Tuning 5` (remote file retrieval, inside web root) | High–Critical **if** the file holds a live secret | Low–Med — real leaks are rarely dupes, but most hits are empty/soft-404 |
| 2 | Default install / default page / admin console | `-Tuning 2` (misconfiguration/default file), `-Tuning e` (administrative console) | Med–High (admin console reachable → High) | Med — common, often already reported |
| 3 | Outdated server / software banner → known CVE | `-Tuning b` (software identification), `Server:`/`X-Powered-By` parsing | Med–High **only** after CVE confirmed | Med–High — banner ≠ exploitable; verify or it is a dupe/invalid |
| 4 | Dangerous HTTP methods (PUT, DELETE, TRACE, WebDAV) | default methods check (in categories `1`/`2`) | Med–High (writable PUT → upload → High) | Med — TRACE/OPTIONS alone is low-value and often N/A |
| 5 | Directory indexing / autoindex listings | `-Tuning 1` | Low–Med (depends what is listed) | High — frequently reported, usually Info unless it exposes sensitive files |
| 6 | Information disclosure / missing security headers | `-Tuning 3` | Low–Info | **Very high** — mass-reported, routinely closed as informational/out-of-scope |
| 7 | Injection smells (XSS / SQLi / command exec) flagged | `-Tuning 4`, `9`, `8` | varies | **High false-positive** — nikto guesses by signature; never report without a hand-built PoC |

The two rows worth real time are **1** (exposed files with live secrets) and **3** (outdated software you can tie to a working exploit). Rows 5–7 are where hunters burn credibility submitting dupes and false positives; treat them as context, not findings.

## Command recipes

Annotated command → expected output. Every flag below is from the Nikto 2.6.1 annotated option list. Low-volume settings are called out.

### Scoped first pass with a paced rate and a saved artifact

```
nikto -h https://target.example.com -Tuning 123b -Pause 1 -maxtime 30m \
  -nointeractive -output pass1.xml -Format xml
```

Expected output: a banner block, then `+`-prefixed finding lines as they are discovered, then a summary. Findings read like:

```
+ Server: Apache/2.4.29 (Ubuntu)
+ /.git/config: Git config file found, may expose repository details.
+ /admin/: This might be interesting.
+ Apache/2.4.29 appears to be outdated (current is at least 2.4.62).
+ OPTIONS: Allowed HTTP Methods: GET, HEAD, POST, PUT, OPTIONS
+ 1 host(s) tested
```

The `.git/config` line and the `PUT` method are leads; the "might be interesting" and outdated-banner lines are leads *pending verification*, not findings.

### Non-standard TLS port

```
nikto -h 198.51.100.10 -port 8443 -ssl -Tuning 123b -Pause 1
```

`-h host:port` is the shorthand; `-ssl` forces TLS mode regardless of port. Use when the service is behind a non-443 HTTPS listener.

### Reverse tuning — run everything except the noisy/dangerous classes

```
nikto -h https://target.example.com -Tuning x469 -Pause 1 -maxtime 1h
```

The `x` inverts the list: this runs **all** categories *except* injection (`4`), DoS (`6`), and SQL injection (`9`) — a fuller sweep that still skips the high-false-positive and host-stressing tests. (`-T` is the documented alias for `-Tuning`.)

### Virtual-host and custom-header scanning

```
nikto -h https://198.51.100.10 -vhost app.example.com \
  -Add-header "X-Bug-Bounty: your-handle" -Tuning 123b -Pause 1
```

`-vhost` sets the `Host:` header so you hit the right app on a shared IP. `-Add-header` (repeatable) is how you comply with programs that require an identifying header on automated traffic — put your researcher handle or ticket ID there so the blue team can attribute the scan.

### Authenticated scan

```
nikto -h https://target.example.com -id "user:pass" -Tuning 123b -Pause 1
```

`-id` supplies HTTP auth as `id:pass` (or `id:pass:realm`). For token/cookie auth, pass the credential with `-Add-header "Authorization: Bearer $TOKEN"` or `-Add-header "Cookie: session=…"` instead.

### Route through an intercepting proxy for evidence capture

```
nikto -h https://target.example.com -useproxy http://127.0.0.1:8080 \
  -Tuning 123b -Pause 1
```

`-useproxy` sends every request through Burp/ZAP so each nikto hit lands in your proxy history with full request/response — exactly the raw material you need for Phase 3 verification and for the report.

### Save the raw positive responses

```
nikto -h https://target.example.com -Tuning 15 -Save evidence/ -output pass.xml -Format xml
```

`-Save <dir>` writes the full server response for every positive to disk (`.` auto-names the directory). Pair it with `-Tuning 15` (interesting files + inside-web-root retrieval) when you are specifically hunting exposed files and want the bodies preserved as evidence.

### Quiet the run down further

```
nikto -h https://target.example.com -Tuning 123b -Pause 2 -timeout 15 \
  -Display V -nolookup -nointeractive -output pass.json -Format json
```

`-Pause 2` doubles the throttle; `-nolookup` skips DNS; `-Display V` adds verbose request logging so you can see exactly what was sent (useful when a program asks for a traffic log). `-Display` also toggles `1` (redirects), `2` (cookies), `3` (all 200s), `4` (auth-needed URLs), `D` (debug), `E` (errors), `P` (progress), `S` (scrub IPs/hostnames from output — handy before sharing a report).

### Target the CGI directories explicitly

```
nikto -h https://target.example.com -Cgidirs all -Tuning 12 -Pause 1
```

`-Cgidirs` controls the CGI-script sweep: `none` skips it, `all` tries every known CGI dir, or pass a specific list (`"/cgi/ /cgi-a/"`). Most modern targets have no CGI surface, so `none` trims pointless requests; use `all` only when the stack (old Apache, Perl/PHP CGI) suggests it is worth the extra volume.

### Scan an app mounted under a sub-path

```
nikto -h https://target.example.com -root /app -Tuning 123b -Pause 1
```

`-root /app` prepends the directory to every request, so nikto's signature paths are tested under the real application root instead of `/`. Essential when the in-scope app lives at `https://host/app/` rather than the domain root — without it every probe hits the wrong path and reports nothing.

### Inspect and scope the plugin/test engine

```
nikto -list-plugins                       # enumerate every test plugin, run nothing
nikto -h https://target.example.com -Plugins "apache_expect_xss;headers" -Pause 1
```

Nikto's tests are plugins; `-Plugins` (default `@@DEFAULT`) runs a named subset, and `-list-plugins` prints the catalog without touching the target. Use this to run one narrow check (e.g. just the `headers` plugin) instead of the whole battery when you are chasing a single lead and want minimal traffic.

### User enumeration via Apache userdirs — scope-gated

```
nikto -h https://target.example.com -mutate 3 -Pause 2
```

`-mutate 3` requests `/~user` paths to enumerate accounts (`4` does the same via cgiwrap; `1` brute-forces files across all root dirs; `2` guesses password-file names). Supply the wordlist with `-mutate-options`. This is **high-volume guessing** — it multiplies request count by your username list — so only run it where the program explicitly permits brute-force/enumeration, and keep `-Pause` high. It is recon for a later attack, not a finding on its own.

### Harden against false positives on catch-all apps

```
nikto -h https://target.example.com -Tuning 1 -404code 302,403 \
  -404string "Page not found" -Pause 1
```

On a site that answers `200`/`302` for everything, tell nikto what a negative looks like: `-404code` lists HTTP codes to always treat as "not found," `-404string` gives a body string (regex allowed) that marks a negative. This is the single most important setting for making the "interesting files" category usable rather than a wall of false positives.

### Override config without editing files

```
nikto -h https://target.example.com -Option "RFIURL=http://198.51.100.5/rfi.php?" \
  -Tuning c -Pause 1
```

`-Option` overrides any `nikto.conf` setting on the command line (repeatable); `-config <file>` points at an alternate config entirely. Useful for pointing remote-file-inclusion/RFI tests (category `c`) at a server *you* control, or setting a proxy/useragent policy per-engagement without touching the global config.

### Validate the install before relying on it

```
nikto -Version          # plugin + database versions
nikto -dbcheck          # check the signature databases for syntax errors
```

Run these once after install or update so you know which database version produced your findings — relevant when a triager asks how current your signature set was.

### Runtime control during a long scan

On POSIX systems, nikto polls the keyboard every ~10 requests (unless `-nointeractive`): **SPACE** prints current status, **v/d/e/p** toggle verbose/debug/error/progress, **r/c/o/a** toggle redirect/cookie/200-OK/auth display, **P** pauses, **N** skips to the next host, and **q** quits gracefully. Use SPACE to check progress without killing the scan, and `q` to stop cleanly if you realize you are hitting something out of scope.

## A note on -evasion (what it is and is not for)

The `-evasion` flag applies IDS/signature-evasion encodings to requests: `1` random non-UTF8 URI encoding, `2` directory self-reference (`/./`), `3` premature URL ending, `4` long random prefix, `5` fake parameter, `6` TAB as request spacer, `7` URL case change, `8` Windows directory separator, `A`/`B` CR/`0x0b` spacers, and `R` (opt-in) a random User-Agent per request from the database. Codes combine: `-evasion 17`.

On a bug bounty program, **do not reach for this to dodge the target's WAF.** Evading a program's own protections is usually against the rules of engagement and can escalate a scoped test into unauthorized activity. Its legitimate use is the reverse: demonstrating that a *documented* control fails to catch an encoded variant, as evidence for a WAF-bypass finding the program explicitly wants tested. Keep the default User-Agent (or an attributed one) so your traffic stays identifiable; `R` deliberately breaks that and should stay off on bounty work.

## Parsing, diffing, and pivoting leads to CVEs

### Choosing an output format

`-Format` accepts `csv`, `json`, `htm`, `txt`, `xml`, `sql`, and `sqld`. Pick by downstream use:

- **`xml`** — the best choice for *diffing* scans over time (stable, line-oriented, greppable).
- **`json`** — for scripted triage, but note the JSON (and XML) schema changed at 2.5.0, so inspect the structure before scripting against it rather than assuming field names:

```
nikto -h https://target.example.com -Tuning 123b -Pause 1 -output pass.json -Format json
jq 'keys' pass.json          # learn the top-level shape first
jq '..|.references? // empty' pass.json   # pull reference IDs once you know the layout
```

- **`htm`** — a readable artifact to attach for a human triager.
- **`csv`** — for a spreadsheet triage grid across several hosts.
- **`sql`** writes INSERT statements; **`sqld`** inserts findings straight into MySQL/PostgreSQL via DBI and needs no `-output` — only worth it if you keep a findings database. Multiple formats at once: `-Format "xml,htm"`.

### Re-scan and diff to catch newly-shipped exposure

Mirroring the "diff the schema on every visit" discipline, re-scan a host periodically and diff the XML: a `.env`, backup, or admin path that appears in a later scan but not an earlier one is freshly-deployed, least-reviewed exposure — often the highest-value, lowest-dupe lead you will get.

```
nikto -h https://target.example.com -Tuning 15 -Pause 1 -output week2.xml -Format xml
diff <(grep -oE '/[^ "]+' week1.xml | sort -u) <(grep -oE '/[^ "]+' week2.xml | sort -u)
```

### From a lead to a reportable CVE

The `references` field (historically the `osvdb` field, renamed in 2.5.0) carries CVE/BID/vulners pointers, not proof. For an outdated-banner or software-identification lead:

1. Pin the exact version from the banner **and** corroborate it (a second fingerprint, a known asset path) — banners are editable and often wrong.
2. Resolve the reference IDs to the specific CVE and read its affected-version range against the pinned version.
3. Confirm the vulnerable *condition* is actually present on this host (the feature is enabled, the path is reachable, the precondition holds) — not just that the version is in range.
4. Only then is it a finding. "Version X has CVE-Y" with no demonstrated condition is the classic nikto dupe/invalid.

## Limitations and where nikto fits

Knowing what nikto does **not** do keeps you from leaning on it for the wrong job:

- **No application-logic testing.** Nikto tests the server and its files, not multi-step flows, authorization, or business logic. BOLA, mass assignment, IDOR, auth bypass — the classes that actually pay on modern programs — are entirely outside its reach. Use a proxy (Burp/ZAP) and manual testing for those.
- **No crawling or spidering.** It probes a static signature list of paths; it does not discover the application's real routes. Pair it with a crawler/content-discovery tool (katana, ffuf, dirsearch) for route discovery, then let nikto check the known-bad paths.
- **High false-positive rate by design.** Signature matching without contextual response analysis means soft-404s, WAF pages, and catch-all routes all read as hits. The `-404code`/`-404string` settings mitigate this; manual verification eliminates it.
- **Trivially fingerprinted.** Static paths, a default User-Agent, and a recognizable cadence make nikto traffic obvious to any WAF or SOC. This is fine for authorized, attributed testing and fatal for anything resembling stealth.

Where it earns its place in a bounty workflow: **after** recon has identified a live, in-scope web host (via nmap/httpx), as a fast first sweep for exposed files, default installs, and outdated-software leads — a complement to, never a replacement for, a crawler and a proxy. Run it once, scoped and paced, mine the few real leads, and move to manual testing.

## Pitfalls, noise and scope discipline

**This is where nikto gets hunters banned or reports rejected.** The failure modes are predictable:

- **Mass-scanning the scope.** Nikto against a list of hosts, or a wildcard, is a high-volume automated sweep with a recognizable fingerprint. Programs with "no automated scanning" or rate-cap clauses treat this as a violation; at best your findings are closed as out-of-scope, at worst your IP is blocked and your account flagged. **One confirmed-in-scope host at a time.**
- **The default battery includes DoS probes.** Tuning category `6` is "Denial of Service." Running the default `-Tuning` (everything) can send requests intended to stress the server. On a bounty target that is both a scope violation and a reliability risk. Always scope with `-Tuning` and leave `6` out; nikto states it does not launch *intentional* DoS attacks, but the category exists and you do not want it firing unattended.
- **Treating OSVDB/reference IDs as findings.** Legacy nikto output cited **OSVDB** identifiers for nearly every hit; OSVDB shut down in 2016. Nikto 2.5.0 removed most OSVDB references and renamed the database `osvdb` field to `references` (now carrying CVE/BID/vulners links), and 2.6.x continued the cleanup — but the historical reputation is deserved: the tool emits a large volume of low-confidence, reference-tagged hits. A reference ID is a pointer to read, not evidence of a vulnerability on *this* host.
- **Reporting the banner.** "Server runs Apache 2.4.29, which is outdated" is not a finding — it is a dupe magnet and frequently closed as informational or invalid. Confirm the version maps to a specific, exploitable CVE and demonstrate the condition before writing it up.
- **False positives on soft-404s and WAFs.** Sites that return `200` for everything (SPA catch-all routes, custom error pages, WAF interstitials) make nikto report every probed path as present. Anchor the run with `-404code` (HTTP codes to always treat as negative, e.g. `-404code 302,403`) and `-404string` (a response-body string/regex that marks a negative) so nikto can distinguish real hits. Without these, the "interesting files" list is pure noise.
- **Injection/SQLi/XSS "findings."** Categories `4`, `8`, `9` are signature guesses, not confirmed exploits. Never submit them as-is; they are leads for a manual or dedicated-tool follow-up only.
- **Ignoring the identifying-header requirement.** Programs that permit scanning often require an attribution header. Use `-Add-header "X-Bug-Bounty: <handle>"` (and keep the default or a clearly-attributed `-useragent`) so your traffic is identifiable — anonymized scanning traffic is what gets blocked.

The one-line discipline: **scope the host, scope the categories, throttle the rate, verify every hit, report nothing nikto found without a manual PoC.**

## Reporting evidence

Nikto's own output lines never go in the report as the finding. Capture, per confirmed lead:

- **The manual PoC.** A `curl -i` (or browser) request/response pair showing the exposed file, the reachable admin console, the accepted dangerous method, or the demonstrated CVE condition — the actual proof, built by hand in Phase 3.
- **Provenance.** One line noting discovery via nikto and the exact command used (with `-Tuning`, `-Pause`, and target), so the triager can see the scan was scoped and paced — evidence of responsible testing, not a blind flood.
- **The saved response body** from `-Save`, or the proxy-history entry (`-useproxy`), as the raw server response behind the PoC.
- **Impact framing.** For an exposed file: what sensitive data it contains (redacted). For outdated software: the specific CVE and why *this* deployment is exploitable. For a dangerous method: what the method lets an attacker do (e.g. `PUT` → arbitrary file write → RCE).
- **What you did not do.** State that you stopped at proof and did not enumerate further, exfiltrate data, or scan beyond the single authorized host — the same restraint the FastAPI methodology's reporting section calls for.

Attach the structured report (`-Format xml`/`json`) as supporting material, but lead with the hand-built PoC. A finding whose only evidence is a nikto line will be closed as unverified.

## References

- Nikto GitHub repository (sullo/nikto) — https://github.com/sullo/nikto
- Nikto wiki, Annotated Option List (2.6.1) — https://github.com/sullo/nikto/wiki/Annotated-Option-List
- Nikto wiki, Scan Tuning — https://github.com/sullo/nikto/wiki/Scan-Tuning
- Nikto wiki, Interactive Features — https://github.com/sullo/nikto/wiki/Interactive-Features
- Nikto 2.5.0 release notes (OSVDB/reference cleanup, `references` field) — https://cirt.net/blog/nikto-2-5-0-released/
- Nikto 2.5.0 potentially-breaking-changes (JSON/XML output, osvdb→references) — https://github.com/sullo/nikto/wiki/2.5.0-Potentially-Breaking-Changes
- Nikto homepage — https://cirt.net/Nikto2

*Research supplement added 2026-11-16, grounded in the Nikto 2.6.1 wiki (Annotated Option List, Scan Tuning, Interactive Features) and the 2.5.0 release notes on OSVDB/reference cleanup (model: claude-opus-4-8). Original line: grounded in corpus command-line evidence (one distinct pattern: `nikto -h` against a running web service); remaining patterns derived from the upstream README and verified against the option parser in `nikto.pl`/`nikto_core.plugin`.*
