---
name: "whatweb"
categories:
  - blackarch-recon
version: "v0.6.4.r0.gd279d93"
homepage: "http://www.morningstarsecurity.com/research/whatweb"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-04-05"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "urbanadventurer/WhatWeb"
github-stars: 6990
github-pushed: "2026-04-02"
github-archived: false
github-issues: 53
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# whatweb

> Fingerprints a website in one request: CMS, web server, framework, JavaScript libraries, email addresses and version numbers.

## What it's for

WhatWeb answers "what is this site built with?". It ships over 1800 plugins, each recognizing one technology — content management systems, blogging platforms, analytics packages, JS libraries, web servers, embedded devices — plus version numbers, email addresses, account IDs and SQL error strings. The default aggression level ("stealthy") needs a single HTTP request per target, which suits scanning public sites; higher levels (`-a 1`..`-a 4`) trade speed for reliability by interrogating the site further (favicon hashes, default files, login pages). It is a passive identifier, not a vulnerability scanner — it tells you what is running so you can pick the next tool.

Commands below are verified against the upstream README.

## Typical use cases

### Fingerprint a single site

Identify the stack behind one URL before deeper testing.

```
whatweb http://www.example.com/
```

Outcome: prints one line per URL with every matched plugin as `Name[value]` pairs — e.g. `HTTPServer[nginx]`, `Title[...]`, `Cookies[...]`, `RedirectLocation[...]` — identifying the web server, framework and CMS in one pass.

### Aggressive scan for exact versions

Confirm a specific CMS and version when the passive pass is inconclusive.

```
whatweb -a 3 http://target.example.com/
```

Outcome: sends additional requests (plugin-dependent probes such as default-installation files) and reports more precise version numbers, e.g. `WordPress[4.9.8]`; noticeably slower and noisier than the default stealthy mode.

### Sweep a subnet quietly

Discover web technologies across many hosts while suppressing dead-address errors.

```
whatweb --no-errors 198.51.100.0/24
```

Outcome: fingerprints every web server in the range, skipping non-responding addresses instead of printing errors; add `--url-prefix https://` to probe HTTPS on each host.

### Batch scan a target list with JSON logging

Feed fingerprinting results into other tooling.

```
whatweb -i targets.txt --log-json=results.json
```

Outcome: reads targets one per line from `targets.txt` and writes structured results to `results.json` while still printing to the terminal; `--log-brief=` gives a greppable one-line-per-host format instead.

## Bug bounty workflow with whatweb

WhatWeb is a pivot engine, not a payout engine. It almost never files a bug on its own — what it does is turn an undifferentiated list of live hosts into a ranked map of *what to test next*: which host runs an outdated CMS with a public CVE, which leaks an email or account ID, which answers with an SQL error string, which exposes an admin or default-install panel. Every phase below keeps you at the stealthy default (`-a 1`, one request per target) until you have a specific reason — and program permission — to escalate.

The single operational fact that governs everything: **aggression level 1 sends one HTTP GET per target and follows redirects; level 3 fires extra probes only when a level-1 plugin already matched; level 4 throws every aggressive plugin at every URL.** The README is explicit that "level 3 aggressive plugins will guess more URLs and perform actions that are potentially unsuitable without permission." Treat `-a 3`/`-a 4` as active scanning, not recon.

### How the plugin model maps to aggression (passive vs aggressive matches)

WhatWeb ships 1800+ plugins, each recognizing one technology. The split that matters for scope discipline is *within* a plugin: a plugin's **passive** logic matches on the single response WhatWeb already fetched — headers, cookies, body regexes, title, favicon bytes in the initial fetch — and runs at `-a 1`. Its **aggressive** logic is what makes *additional* requests: guessing default-install paths, fetching login pages, probing version-specific files. That logic is gated behind aggression level, which is why level 1 is one request and level 3 only escalates "if a level 1 plugin is matched" (README). The valid levels are `1` (Stealthy), `3` (Aggressive), `4` (Heavy) — there is no level 2.

Consequence for a hunter: everything you can learn *passively* costs exactly one request per host and is program-safe by default. The moment you want an exact version that only a default-install file or admin page reveals, you are opting into the aggressive request set — so scope it to the one plugin (`-p <name>`) that needs it rather than letting all 1800 escalate. Use `-I <plugin>` (`--info-plugins`) to read exactly what a plugin matches and which requests its aggressive mode makes *before* you unleash it.

### Phase 0 — Scope, safety, and baseline

Goal: confirm the hosts are in scope and pin WhatWeb to passive behavior before the first request leaves your machine.

- Read the program's rules of engagement for rate limits and whether automated scanning is allowed at all. WhatWeb at `-a 1` is a single request per host — about as light as any active tool gets — but a CIDR sweep (`198.51.100.0/24`) is still hundreds of requests and can trip rate monitors. Scan only hosts and ranges the program lists.
- Default aggression is already `1` (stealthy) — you do not need to pass `-a 1`, but passing it explicitly documents intent in your command history and reports.
- Change the User-Agent off the default. WhatWeb ships a Snort IDS signature match for its default `WhatWeb/0.6.4` agent; the README notes "changing the user-agent using the `-U` or `--user-agent` command line option will avoid the Snort IDS rule for WhatWeb." A WAF that flags the default agent can taint the whole engagement.

```
whatweb -a 1 -U 'Mozilla/5.0 (research; authorized testing)' https://www.example.com/
```

### Phase 1 — Passive fingerprint sweep

Goal: fingerprint every live host at one request each, feeding the host list straight from your discovery tooling.

The canonical bug-bounty pipeline is httpx (live-host probing) → whatweb (tech profiling). WhatWeb reads targets from stdin via the documented `-i /dev/stdin`, so no temp file is needed:

```
httpx -silent -l hosts.txt | whatweb -i /dev/stdin -a 1 --no-errors -t 25 --log-json=fp.json
```

- `--no-errors` suppresses the dead-host/connection-refused noise that otherwise drowns a large sweep (it lives under OUTPUT in the help, not target selection).
- `-t 25` is the default thread count; lower it (`-t 5`, or `-t 1 --wait=1`) when the program caps request rate or when you want the sweep to look like organic traffic.
- `--log-json=fp.json` captures structured output for the triage phase while the terminal still shows the live one-line summaries.

### Phase 2 — Triage the fingerprints

Goal: turn the fingerprint dump into a short list of hosts worth manual attention. This is where WhatWeb's value is realized.

Pull the high-signal rows out of the JSON log (every plugin match is a `Name[value]` pair):

```
jq -r '.[] | [.target, (.plugins | keys | join(","))] | @tsv' fp.json
```

Read the output the way it is structured: each host line is `URL [HTTP-STATUS] Plugin[value], Plugin[value], ...`. A concrete example and what each token is worth:

```
www.example.com [200 OK] Apache[2.4.29], Country[UNITED STATES][US],
  Email[admin@example.com], HTTPServer[Ubuntu Linux][Apache/2.4.29 (Ubuntu)],
  PHP[5.6.40], WordPress[4.9.8], X-Powered-By[PHP/5.6.40]
```

Three separate leads sit in that one line: `WordPress[4.9.8]` and `PHP[5.6.40]` are both years out of date and map to published CVEs (version → CVE lookup); `Email[admin@example.com]` is a harvested address for OSINT/account chains; `HTTPServer[Ubuntu]` plus the pinned Apache and PHP builds narrows the OS and the patch window. None of these is a bug by itself — each is the *first* link of a chain you then complete with the right tool.

Prioritize, in order:
1. **Version-stamped software** — any `WordPress[4.9.8]`, `Drupal[...]`, `Jenkins[...]`, `PHP[5.x]`, `nginx[1.x]` with a concrete version is a direct CVE lookup. An end-of-life or known-vulnerable version on an in-scope host is the most common whatweb-to-bounty path.
2. **Leaked identifiers** — WhatWeb plugins extract email addresses, analytics/account IDs, and similar artifacts from page bodies. These feed OSINT and account-takeover chains.
3. **Error strings** — SQL error fragments and framework stack-trace signatures in the fingerprint point at injection or verbose-error findings to confirm by hand.
4. **Admin / default-install surfaces** — login panels, default CMS install pages, and dev frameworks indicate pre-auth attack surface.

Filter a large sweep for one technology without re-scanning using `--grep`/`-g`, which shows only matching results:

```
whatweb -i /dev/stdin -a 1 --no-errors -g WordPress < hosts.txt
```

### Phase 3 — Targeted aggressive confirmation (permission required)

Goal: pin an exact version on *one* host when the passive pass was inconclusive — and do it with the fewest possible requests.

When you escalate, escalate narrowly. The README's own guidance: "it is advisable... to select a specific plugin when attempting to fingerprint software versions in aggressive mode. This approach is far more stealthy as it will limit the number of requests." So pair `-a 3` with `-p <plugin>` instead of letting every aggressive plugin fire:

```
whatweb -a 3 -p wordpress https://blog.example.com/
```

- `-a 3` only runs aggressive probes for plugins that already matched passively, so scoping it to a single plugin keeps the extra requests to a handful rather than a flood.
- Never open an engagement at `-a 4` against production. Level 4 "makes a lot of HTTP requests per target" with "aggressive tests from all plugins... for all URLs" — it is a scanning event, loud enough to alert a SOC and heavy enough to matter on fragile infrastructure.
- WhatWeb has no request cache: "if you use aggressive plugins on redirecting URLs you may fetch the same files multiple times." On a redirect-heavy target, aggressive mode multiplies requests silently. Watch the redirect behavior with `--follow-redirect=same-site` (or `never`) to contain it.

### Phase 4 — Pivot to exploitation tooling

Goal: hand the confirmed fingerprint to the tool that actually tests the vulnerability.

WhatWeb's job ends at "this host runs X version Y." The finding comes from the next tool:
- Outdated CMS/plugin version → version-specific CVE lookup, then `nuclei` templates or `wpscan` for WordPress.
- Leaked emails/IDs → OSINT and credential-stuffing-adjacent checks (only if the program allows).
- SQL error strings → manual injection confirmation or `sqlmap` against that one parameter.
- Exposed admin/default panels → manual auth testing.

Feed a recon database for larger programs with the SQL or Elasticsearch loggers so fingerprints are queryable alongside your other recon data:

```
whatweb -i /dev/stdin -a 1 --no-errors --log-sql=whatweb.sql < hosts.txt
```

### Phase 5 — Capture evidence as you go

Always log to a file in parallel with the terminal output. `--log-verbose=` records the plugin descriptions and every matched value; `--log-json=`/`--log-xml=` give machine-readable artifacts for the report. These timestamped logs are what you attach — see *Reporting evidence* below.

## Where the money is with whatweb

WhatWeb output is a *lead generator*. The table ranks what a fingerprint line most often unlocks, the feature that surfaces it, and how likely the eventual finding is to be a duplicate. Severity is the severity of the *downstream* finding, not of running whatweb.

| Finding class the fingerprint unlocks | WhatWeb feature that reaches it | Typical downstream severity | Dupe risk |
|---|---|---|---|
| Outdated CMS/framework with a public CVE (WordPress, Drupal, Jenkins, etc.) | `-a 3 -p <plugin>` to pin the exact version; version appears as `Name[x.y.z]` | High–Critical (if a working CVE exists) | High on flagship hosts; low on forgotten subdomains |
| End-of-life server/language (`PHP[5.x]`, old `nginx`/`Apache`) | Passive `-a 1`; version in `HTTPServer[...]`/`X-Powered-By[...]` | Medium–High | High — everyone's scanner sees it |
| Exposed admin / default-install / setup pages | Plugin match for the panel at `-a 1`; confirm with targeted `-a 3` | High (if pre-auth) | Medium |
| Leaked email addresses / account IDs | Email and account-ID plugins extract them from the body at `-a 1` | Low–Medium (feeds chains) | Medium |
| SQL error strings / stack traces (verbose errors) | Error-signature plugins flag them; confirm injection by hand | Medium–High | Low once confirmed |
| Analytics/tenant IDs that correlate assets | ID-extraction plugins; `--grep` across a sweep to cluster shared IDs | Low (info / asset mapping) | High as standalone, valuable as glue |
| Technology inventory gaps (shadow stacks on odd subdomains) | Large `-i` sweep with `--log-json`, diff against the known asset list | Info → feeds higher findings | Low — this is original recon |

The honest framing for reports: **a bare WhatWeb fingerprint is almost always a duplicate or an informational note.** The payout comes when the fingerprint is the first link in a chain you then *complete* — an exact vulnerable version plus a working PoC, a leaked ID plus the account it unlocks, an SQL error plus a confirmed injection. Report the chain, cite WhatWeb as the discovery step, never submit "WhatWeb says you run nginx" on its own.

## Command recipes

Each block is a real command with the low-volume setting called out. Targets use `example.com` and the TEST-NET range `198.51.100.0/24`.

**Single-host stealth fingerprint (the default, one request):**
```
whatweb -a 1 -U 'authorized-research' https://www.example.com/
# -> www.example.com [200 OK] Country[...], HTTPServer[nginx], Title[...],
#    X-Powered-By[PHP/8.1.2], nginx, PHP[8.1.2]
```
One GET, redirects followed. The `X-Powered-By[PHP/8.1.2]` and `nginx` lines are your CVE lookup seeds.

**Pipeline from httpx, structured output, dead hosts silenced:**
```
httpx -silent -l scope-hosts.txt | whatweb -i /dev/stdin -a 1 --no-errors -t 25 --log-json=fp.json
```
`-i /dev/stdin` is the documented way to pipe targets. `--no-errors` drops connection failures. Lower `-t` for rate-limited programs.

**Rate-limited, single-threaded, paced sweep (the gentlest profile):**
```
whatweb -i scope-hosts.txt -a 1 -t 1 --wait=2 --no-errors --log-brief=fp.txt
```
One thread, two seconds between connections. The README notes "`--wait` and `--max-threads`... can be used to assist in IDS evasion" — here the goal is simply staying under a rate cap and looking like organic traffic. `--log-brief` gives one greppable line per host.

**Targeted version confirmation, minimal extra requests:**
```
whatweb -a 3 -p wordpress https://blog.example.com/
```
Aggressive probes fire only for WordPress, only because it already matched passively. Scoping `-p` to one plugin is the README's recommended way to keep aggressive mode stealthy.

**Contain redirect amplification in aggressive mode:**
```
whatweb -a 3 -p drupal --follow-redirect=same-site https://cms.example.com/
```
WhatWeb has no cache, so aggressive plugins on redirecting URLs can refetch the same files repeatedly. `--follow-redirect=same-site` (values: `never`, `http-only`, `meta-only`, `same-site`, `always`) stops off-site redirect chains from multiplying requests.

**Probe HTTPS across an in-scope range, errors suppressed:**
```
whatweb --no-errors --url-prefix https:// 198.51.100.0/24
```
`--url-prefix` prepends the scheme so each host is hit over HTTPS. Only run against ranges the program explicitly lists — a CIDR sweep is hundreds of requests.

**Check one path across a host list with `--url-pattern`:**
```
whatweb -i scope-hosts.txt --url-pattern '%insert%/crossdomain.xml' -p crossdomain_xml
```
`--url-pattern` requires `--input-file` and substitutes each target into `%insert%`. Scoping `-p` to the one relevant plugin avoids firing the whole plugin set at the extra path.

**Filter a large scan to one technology without re-scanning:**
```
whatweb -i scope-hosts.txt -a 1 --no-errors -g 'Jenkins'
```
`-g`/`--grep` shows only results matching the string or `/regex/`, so you see just the Jenkins hosts.

**Feed a recon database (SQL or Elasticsearch):**
```
whatweb -i scope-hosts.txt -a 1 --no-errors --log-sql=whatweb.sql
whatweb -i scope-hosts.txt -a 1 --no-errors --log-elastic-host=127.0.0.1:9200 --log-elastic-index=whatweb
```
Make the fingerprints queryable alongside the rest of your recon. (MongoDB logging via `--log-mongo-*` also exists but requires the `mongo` and `rchardet` gems plus the Charset plugin, which carries a CPU penalty — see Pitfalls.)

**Tune output for a large batch sweep:**
```
whatweb --output-buffer-size=50 -t 100 --log-verbose=detailed.log scope-hosts.txt
whatweb --output-sync -t 100 --log-brief=results.txt scope-hosts.txt
```
On a big host list, `--output-buffer-size=SIZE` (0 = unbuffered, default auto-sizes to the thread count) keeps terminal output coherent under high `-t`, and `--output-sync` forces immediate flushing for real-time monitoring (slower at high thread counts). Reserve `-t 100` for ranges you are authorized to scan hard; keep `-t` low on a single host. Setting `WHATWEB_PROFILE=1` in the environment prints timing diagnostics if a sweep runs slow.

**Understand what a plugin actually detects before you trust it:**
```
whatweb -I wordpress        # --info-plugins: detailed description, matches, dorks
whatweb --search-plugins=cms # keyword-search the plugin set
```

**Fingerprint an authenticated area (reveals the stack behind login):**
```
whatweb -a 1 -c 'session=<your-own-test-session>' https://app.example.com/dashboard
whatweb -a 1 -u 'testuser:testpass' https://app.example.com/admin/
```
`-c`/`--cookie` replays a cookie string; `-u`/`--user` does HTTP basic auth (the value must contain a `:`). Use only your own authorized test credentials — the authenticated surface often runs a different, chattier stack than the marketing front end.

**Capture full request/response pairs through Burp for evidence:**
```
whatweb -a 1 --proxy 127.0.0.1:8080 https://www.example.com/
```
`--proxy hostname[:port]` routes every WhatWeb request through your intercepting proxy, so the exact HTTP exchange that produced a fingerprint is saved in Burp's history for the report. `--proxy-user user:pass` adds proxy auth.

**One-off detection of a specific string across a sweep (`--custom-plugin`):**
```
whatweb -i scope-hosts.txt -a 1 --no-errors \
  --custom-plugin ":text=>'Powered by LegacyPortal'"
```
Defines an ad-hoc plugin (named `Custom-Plugin`) matching a text string, MD5, or version regex (`:md5=>'...'`, `:version=>/.../`) — handy for hunting one vendor string the built-in plugins do not cover, without writing a plugin file.

**Pull Google dorks for a confirmed technology (OSINT pivot):**
```
whatweb --dorks wordpress
```
`--dorks=PLUGIN` lists the Google dorks associated with a plugin — a quick pivot from "this host runs X" to finding other exposed instances or related assets in scope.

### WhatWeb's place among fingerprinters

WhatWeb is not the only tech-fingerprinter in a modern recon chain, and knowing where it wins keeps you from redundant scanning:

- **httpx `-td`/`--tech-detect`** already tags technologies (via Wappalyzer fingerprints) during live-host probing, so a plain httpx pass gives you a first-cut stack for free. Reach for WhatWeb when you want *version extraction*, its email/account-ID/SQL-error plugins, or the aggressive confirmation path httpx does not offer. The common pattern — httpx for liveness + quick tags, then WhatWeb on the interesting subset — is why `httpx ... | whatweb -i /dev/stdin` is the canonical pipeline.
- **Wappalyzer / Nuclei tech templates** overlap on CMS and framework detection. WhatWeb's edge is breadth of plugins and the single-request stealth default; Nuclei's edge is going straight from detection to a CVE check. Fingerprint with WhatWeb, then hand the version to Nuclei.
- **Nothing here is a vulnerability scanner.** WhatWeb explicitly "does not support any intrusion/exploit level tests" — it tells you *what* to test, and the payout comes from the tool you point at it next.

## Pitfalls, noise and scope discipline

**`-a 3`/`-a 4` are active scanning, not recon.** The biggest reason WhatWeb use gets reports rejected or hunters warned is treating aggressive mode as passive. Level 3 "guesses more URLs and performs actions that are potentially unsuitable without permission"; level 4 "makes a lot of HTTP requests per target" with aggressive tests from *all* plugins against *all* URLs. Default to `-a 1`, escalate to `-a 3` only on a specific host with a specific plugin, and reserve `-a 4` for lab or explicitly-authorized stress scenarios.

**Level 2 does not exist.** The aggression values are `1`, `3`, and `4`; passing `-a 2` raises `Aggression level must be 1, 3, or 4` and aborts. The "1..4" shorthand is misleading — there is no passive-intermediate level between stealthy and aggressive.

**No request cache means redirect loops multiply requests.** "WhatWeb has no caching so if you use aggressive plugins on redirecting URLs you may fetch the same files multiple times." On redirect-heavy targets, aggressive mode can quietly 3–5x your request count. Constrain it with `--follow-redirect=same-site`/`never` and `--max-redirects`.

**Thread count is a rate-limit hazard.** The default `-t 25` means 25 concurrent connections. Against a single small host or a program with a request cap, drop to `-t 1 --wait=N`. A 25-thread sweep across a CIDR can look like a DoS to monitoring.

**CIDR and range sweeps can exceed scope.** `198.51.100.0/24` expands to 254 hosts × one request each at `-a 1`, more at higher levels. Scan only ranges the program lists, and the README's efficiency note applies: "it is much more efficient to use a port scanner like masscan to discover which have port 80 open before scanning with WhatWeb" — which also shrinks your footprint to live web hosts.

**The default User-Agent is IDS-signatured.** Snort ships a rule matching `WhatWeb`'s default agent; leaving it in place can get your traffic flagged or your source IP blocked, poisoning the engagement. Set `-U` to something neutral.

**JSON/MongoDB logging has a CPU cost.** Character-set detection via the Charset plugin "dramatically decreases performance by requiring more CPU," and it is required by JSON and MongoDB logging. On a large sweep this slows the scan and raises load — use `--log-brief`/`--log-xml` when you do not need JSON.

**WhatWeb is not a vulnerability scanner.** It fingerprints; it "currently does not support any intrusion/exploit level tests in plugins." A fingerprint is not proof of exploitability. Reporting "you run WordPress 4.9.8" without demonstrating a working issue is an informational note at best and a duplicate at worst — confirm the vulnerability with the appropriate tool before submitting.

**Fingerprints can be wrong or stale.** Reverse proxies, CDNs, and `Server`/`X-Powered-By` header spoofing all mislead WhatWeb. Corroborate a version from a second signal (behavioral quirk, a versioned asset path, an error page) before you build a report on it.

## Reporting evidence

WhatWeb's output is recon context, so in a report it supports the finding rather than being the finding. Capture:

- **The exact command**, including the aggression level and User-Agent, so the triager can see you stayed within program limits (`whatweb -a 1 -U '...' https://host.example.com/`). This also proves you did not run an aggressive scan without permission.
- **The relevant `Name[value]` lines** verbatim — the specific `WordPress[4.9.8]`, `X-Powered-By[PHP/5.6.40]`, or SQL-error plugin match that seeds the finding. Trim unrelated plugin noise.
- **A structured log artifact.** `--log-json=` or `--log-xml=` gives a timestamped, machine-readable record; `--log-verbose=` adds the plugin descriptions and all matched values. Attach the log rather than only a terminal screenshot.
- **The request/response pair** that carried the signal — the HTTP response showing the version header or error string WhatWeb keyed on — so the finding does not rest on WhatWeb's say-so alone.
- **The completed chain**, not just the fingerprint: the fingerprint line → the CVE or weakness it maps to → the PoC that confirms it. WhatWeb is the "how I found the version" step; the PoC is the finding.
- **Scope-discipline evidence**: that you scanned only in-scope hosts, at what rate (`-t`/`--wait`), and that aggressive probes (if any) were run against a single authorized host with a scoped `-p`.

Never paste a raw multi-host `--log-json` dump into a report as though the inventory itself were the vulnerability — distill it to the one host and one line that matter.

## References

- WhatWeb README (usage, aggression levels, examples, performance notes), urbanadventurer/WhatWeb, master branch — https://raw.githubusercontent.com/urbanadventurer/WhatWeb/master/README.md
- WhatWeb main executable (verbatim `GetoptLong` option definitions and `usage_full` help text, v0.6.4) — https://raw.githubusercontent.com/urbanadventurer/WhatWeb/master/whatweb
- WhatWeb project page — https://morningstarsecurity.com/research/whatweb/
- WhatWeb repository — https://github.com/urbanadventurer/WhatWeb

*Research supplement added 2026-11-16, grounded in the upstream WhatWeb README and `whatweb` executable (v0.6.4, urbanadventurer/WhatWeb) — every flag, aggression level, and default verified against that source (model: claude-opus-4-8).*
