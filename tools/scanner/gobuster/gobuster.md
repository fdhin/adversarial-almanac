---
name: "gobuster"
categories:
  - corpus-scanner
homepage: "https://github.com/OJ/gobuster"
origin: "corpus"
github-repo: "OJ/gobuster"
packaged-by: [arch, nixpkgs]
arch-builddate: "2025-09-04"
meta-checked: "2026-11-09"
github-stars: 14292
github-pushed: "2026-11-09"
github-archived: false
github-issues: 26
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# gobuster

> Wordlist-driven brute-forcer for hidden web content — directories, files and virtual hosts above all, with DNS, cloud-bucket and fuzz modes alongside.

## What it's for

Gobuster fires a wordlist at a target and reports which entries produce an interesting response. Its bread and butter is dir mode: discovering directories and files on a web server far faster than any spider, which is why it is usually the first directory brute-forcer reached for during web recon. Around that core it also enumerates virtual hosts (vhost mode), DNS subdomains (dns mode), AWS S3 and Google Cloud Storage buckets (s3/gcs), TFTP files, and arbitrary FUZZ positions in URLs, headers and bodies (fuzz mode). It finds things but does not exploit them, and it never parses page content — a hit is judged purely by HTTP status code and response size, so servers that answer 200 to everything need status/length filters before results mean anything. Since v3.0 a mode subcommand is mandatory (`dir`, `dns`, `vhost`, `fuzz`, `s3`, `gcs`, `tftp`); bare `gobuster -u ... -w ...` invocations in older material are pre-2020 v2 syntax that current versions reject.

## Typical use cases

### Enumerate hidden directories and files

Sweep a web root with a wordlist as the opening move of web enumeration.

```
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt
```

Outcome: Prints a configuration banner and a live progress line, then every wordlist entry that survives the default 404 filter as `/path (Status: 301) [Size: 312]`. Browse the hits manually, and to go deeper re-run the same command with `-u` pointed at a discovered directory — nesting scans like this is the standard workflow since the tool does not recurse on its own.

### Hunt for files by extension

Append extensions to every guess to surface scripts and backups that directory words alone never hit.

```
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt -x php,html,bak
```

Outcome: Each wordlist entry is additionally requested as `word.php`, `word.html` and `word.bak`, so files like `/config.php` or `/index.bak` appear in the results alongside plain directories. Extension lists worth trying grow from the stack in play (asp,aspx for IIS; js for static asset dirs).

### Filter results by HTTP status

Blacklist the status codes that drown the output so what remains is actionable.

```
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt -b 403,404
```

Outcome: 404 responses are already suppressed by default; this extends the blacklist to 403 as well, so denied-and-gone paths vanish and remaining statuses (200, 301, 401, ...) are all worth reading. The inverse works too: `--status-codes` (`-s`) whitelists what to show, e.g. `-s 200,204,300-308` — both flags accept comma lists and ranges.

### Tune scan speed

Raise concurrency against fast targets, or throttle down when the server objects.

```
gobuster dir -u http://example.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100
```

Outcome: Runs 100 concurrent requests instead of the default 10, draining a medium wordlist in minutes rather than hours. If resets or timeouts start scrolling past, halve the thread count or switch to a pacing delay (`--delay 500ms`) — slower scans also stay quieter in logs.

### Follow redirects during enumeration

```
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt -r
```

Outcome: Requests that redirect are followed to the destination and the final status is reported — catches content living behind 301/302 hops instead of logging every path as a bare redirect.

### Scan HTTPS hosts with invalid certificates

```
gobuster dir -k -u https://198.51.100.10:8443 -w /usr/share/wordlists/dirb/common.txt
```

Outcome: Certificate validation is skipped (`-k`), so internal and dev TLS endpoints with self-signed or expired certificates enumerate normally instead of every request failing the handshake.

### Enumerate API paths with POST requests

```
gobuster dir -m post -u http://example.com/api/ -w /usr/share/wordlists/dirb/common.txt -b 404,405
```

Outcome: Every guess is submitted as a POST and the response status judged, so REST APIs that answer all GET fuzzing with a uniform 405 become readable once 404 and 405 are blacklisted — routes that really process the request stand out.

### Reach targets through an authenticating proxy

```
gobuster dir -u http://target.example.com -w /usr/share/wordlists/dirb/common.txt --proxy 'http://user:Password1@198.51.100.10:3128'
```

Outcome: All requests are tunnelled through the proxy with those credentials, enumerating a site reachable only from inside the proxy's network. Special characters in the password must be URL-encoded (`%3e` for `>`, `%40` for `@`) or the proxy URL fails to parse.

### Scan with a session cookie

```
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt -c 'session=SESSION_TOKEN'
```

Outcome: The cookie rides on every request, so paths gated behind a logged-in session (admin areas, action endpoints) enumerate under your authenticated identity. Arbitrary headers work the same way via `-H 'Authorization: Bearer TOKEN'`.

### Brute-force virtual hosts

Discover which hostnames a single IP actually serves.

```
gobuster vhost -u http://example.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
```

Outcome: Each word is tried as a `Host:` header (dev.example.com, staging.example.com, ...) and changes in response size or status reveal which names the server serves distinctly — every discovered vhost then becomes a new enumeration target. Current versions filter the redirect noise directly (`--exclude-status 302`, `--exclude-length`); on older ones pipe the output through `grep -v 302`.

### Quiet output for scripts and logs

```
gobuster dir -q -u http://example.com -w /usr/share/wordlists/dirb/common.txt
```

Outcome: The banner, configuration dump and progress noise are suppressed and only found paths print — clean output for piping into other tools, grepping, or pasting into a report.

### Save results to a file

```
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt -o gobuster-example.txt
```

Outcome: Finds are written to the named file as well as the terminal (progress output automatically disables when redirected), preserving an ordered record per URL scanned — useful when working through several hosts, ports or vhosts in one session.

## Bug bounty workflow with gobuster

Gobuster is a discovery tool, not an exploit tool: it tells you *what exists* on a target, and every finding it produces is an input to some other test. On a bug bounty program the value is in reaching assets the program owner forgot about — a stale vhost, a backup file, an undocumented API route, a world-readable bucket — before anyone else does, while staying inside the rate and scope rules. The modes map to attack-surface layers, so the natural order is outside-in: subdomains → vhosts on each IP → content on each host → files on each directory → buckets referenced anywhere. Treat the whole thing as single-pass, low-thread, evidence-logged work; the brute-force verbs below are the loudest thing in this tools vault, and a program's WAF or rate monitor will notice a 100-thread sweep long before it finds anything.

**A footgun to internalise before Phase 0:** short flags are not stable across modes. `-d` is `--delay` globally, but the domain flag in `dns` and `vhost` modes is `--domain`/`-do` (two letters). `gobuster dns -d example.com` does **not** set the domain — it tries to parse `example.com` as a duration, errors, and if you fat-finger it into a working form you may silently scan nothing. Likewise `-m` is `--method` in dir/vhost but `--max-files` in s3/gcs, `-c` is `--cookies` in dir but `--check-cname` in dns, and `-s` is `--status-codes` in dir but `--show-files` in s3/gcs. Read the per-mode help (`gobuster dir --help`) once per mode rather than carrying short flags across them.

### Phase 0 — Scope lock, wordlists, and rate budget

Goal: fix the in-scope hosts/IPs, pick wordlists proportional to the engagement, and decide a thread/delay budget you will not exceed.

- **Confirm scope covers brute force.** Many programs allow content discovery but cap request rate or forbid automated scanning of specific hosts. Directory and DNS brute force are *active* and *high-volume* by nature — a medium wordlist is 220k+ requests per host. If the policy says "no automated scanning" or "max N req/s", gobuster's default 10 threads with no delay will blow past it. There is no native requests-per-second limiter; your only levers are `--threads`/`-t` and `--delay`/`-d` (a per-thread wait), so effective rate ≈ threads ÷ (delay + response time). To hold roughly one request per second: `-t 1 --delay 1s`.
- **Right-size the wordlist.** Start small and specific, escalate only if the small list pays. `raft-small-*` and `common.txt` before `directory-list-2.3-medium.txt`; `subdomains-top1million-5000.txt` before `-110000`. Oversized lists on a rate-capped program waste your budget and the owner's logs for mostly-dupe hits.
- **Baseline the target's "not found" behaviour.** Before trusting any hit, learn what a guaranteed-miss returns. Request a random path and a random host and record status + body length — if the server soft-404s (returns 200 with a boilerplate body for everything), you will need `--exclude-length`/`-xl` or a blacklist, because gobuster judges hits by status code and response size *only*; it never reads page content.

### Phase 1 — Subdomain sweep with dns mode (quick active brute)

Goal: a fast, in-process subdomain brute to widen the host list. `dns` mode is a *quick brute*, not a recon suite — it sends live DNS queries for `word.domain` and reports what resolves. It has no passive sources, no permutation engine, no recursion; use amass/subfinder for passive breadth and feed gobuster the remainder, or run it standalone when you just want a fast wordlist brute.

```
gobuster dns -do example.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t 20 --wildcard
```

- `--domain`/`-do` is the target (required); the wordlist supplies the left-hand labels.
- `--wildcard`/`-wc` forces continued operation when a wildcard record is detected. Without it, gobuster detects `*.example.com` resolving and **aborts** to avoid a flood of false positives — which is the safe default, but on wildcard domains you must either accept the abort or pass `--wildcard` and then filter the noise yourself.
- `--check-cname`/`-c` additionally reports CNAME targets — valuable because a subdomain CNAMEd to a decommissioned cloud resource is a subdomain-takeover lead.
- `--resolver 1.1.1.1` pins a resolver (format `server` or `server:port`); `--protocol tcp` switches off UDP (only honoured when `--resolver` is set). `--no-fqdn`/`-nf` stops gobuster adding the trailing dot, so the system search domains apply — rarely what you want against an external target.
- `--timeout`/`-to` defaults to 1s here (not the HTTP 10s).

Every host that resolves becomes a Phase 2/3 target. CNAME chains pointing at `*.s3.amazonaws.com`, `*.github.io`, `*.herokudns.com`, etc. with an NXDOMAIN or "no such bucket" behind them go straight to a takeover check.

### Phase 2 — Virtual host discovery with vhost mode

Goal: find hostnames a single IP serves that DNS never advertised — the classic shared-hosting / reverse-proxy blind spot. `vhost` mode holds the IP/URL constant and varies the `Host:` header, so it surfaces `staging.example.com`, `internal-api.example.com`, or an old customer's site co-tenanted behind the same front end — none of which a DNS brute (Phase 1) would reach if they have no public A record.

```
gobuster vhost -u https://example.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain --exclude-length 0 -k
```

- `--append-domain`/`-ad` appends the URL's base domain to each wordlist word (so `dev` → `Host: dev.example.com`). **Without it, the wordlist must already contain fully-qualified names.** This flag changed meaning across versions and is the single most common vhost-mode mistake; current gobuster warns if it looks like you forgot it.
- Filtering is everything in vhost mode, because a default front end answers *every* unknown host identically. Use `--exclude-length`/`-xl` to drop that boilerplate response size, or `--exclude-status`/`-xs` to drop its status code (ranges supported, e.g. `-xs 301,302,404`). When the boilerplate length varies with the hostname echoed back into the page, `--exclude-hostname-length`/`-xh` auto-adjusts the excluded length per response (it requires `--exclude-length` to be set).
- `--domain`/`-do` lets you point `-u` at a bare IP and still control the appended domain: `gobuster vhost -u https://198.51.100.10 --domain example.com --append-domain ...`.
- A vhost that answers with a *different* size or status than the baseline is serving distinct content for that name. Re-request it with `curl -H 'Host: found.example.com'` to confirm, then treat it as a fresh host for Phase 3.

### Phase 3 — Content discovery with dir mode

Goal: enumerate directories and files on each confirmed host. This is gobuster's core and usually the first web step. Judge hits by status and size, filter the noise, and remember the tool does not recurse.

```
gobuster dir -u https://example.com -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -t 20 -k -o dir-example-root.txt
```

- `--status-codes`/`-s` (whitelist) and `--status-codes-blacklist`/`-b` (blacklist) are mutually overriding: the default is `-b 404`, and if both are set the blacklist wins. To switch to a whitelist you must *clear* the blacklist: `-s 200,204,301,302,307,401,403 -b ""`.
- `--extensions`/`-x php,txt,bak` requests each word with those suffixes too; `--extensions-file`/`-X` reads the extension list from a file. `--add-slash`/`-f` appends `/` to each request (surfaces directories that only answer with a trailing slash).
- `--exclude-length`/`-xl` drops soft-404s by body size (comma lists and ranges like `203-206`). `--expanded`/`-e` prints full URLs (paste-ready); `--no-status`/`-n` and `--hide-length`/`-hl` trim the columns.
- `--follow-redirect`/`-r`, `--no-tls-validation`/`-k`, `--cookies`/`-c`, `--headers`/`-H 'Authorization: Bearer …'`, `--method`/`-m`, `--proxy`, `--useragent`/`-a`, `--random-agent`/`-rua` cover auth'd and proxied scans (route through Burp with `--proxy http://127.0.0.1:8080` to capture every request/response for evidence).
- **No recursion.** Gobuster requests exactly one URL per word and never re-enqueues a discovered directory (confirmed in `gobusterdir/gobusterdir.go` — there is no recurse logic anywhere in the mode). Going deeper is a manual loop: take each `301`/`200` directory hit and re-run `dir` with `-u` pointed at it. If you want automatic recursion, that is feroxbuster's and ffuf's job (`ffuf -recursion`, feroxbuster recurses by default) — see the tradeoff note below.

### Phase 4 — Backup, source, and secret-file hunting

Goal: the highest-signal, lowest-dupe use of dir mode — files developers left behind. Backup and VCS artifacts frequently contain source, credentials, or config, and they are under-hunted because default wordlists miss them.

```
gobuster dir -u https://example.com -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt \
  -x bak,old,zip,tar.gz,sql,swp,inc,config -b 404 --discover-backup -k -o files-example.txt
```

- `--discover-backup`/`-db`: on each file hit, gobuster automatically re-requests it with a battery of backup extensions — so a discovered `config.php` triggers probes for `config.php.bak`, `config.php~`, etc. This is the single most cost-effective flag in the tool for secret recovery.
- Pair with targeted small wordlists for `.git/`, `.svn/`, `.env`, `.DS_Store`, `wp-config.php.bak`, `/backup/`, `/backups/`. A reachable `.git/config` or `.env` is usually a direct Medium–High finding (source/secret disclosure), and far less likely to be a duplicate than a generic `/admin` hit.

### Phase 5 — Cloud bucket enumeration with s3 and gcs modes

Goal: find world-readable (or writable) AWS S3 / Google Cloud Storage buckets tied to the target's naming conventions.

```
gobuster s3 -w bucket-candidates.txt --max-files 10
gobuster gcs -w bucket-candidates.txt -m 10
```

- The wordlist holds **bucket name candidates**, not paths. Generate them from the org's naming patterns: `example`, `example-prod`, `example-backups`, `example-assets`, `example-dev`, `static-example`, `example-user-uploads`, plus environment/region permutations. gobuster checks each for public listing.
- `--max-files`/`-m` (default 5) caps how many object keys are listed per found bucket — raise it to sample more; `--show-files`/`-s` (default true) prints the keys. These modes take the `BasicHTTPOptions` (proxy, timeout, user-agent) but not the HTTP auth/cookie set.
- A publicly *listable* bucket is Low–Medium on its own; a bucket holding PII, backups, or credentials is High; a *writable* bucket (test with the AWS CLI, with permission) is often Critical. Confirm and demonstrate impact with `aws s3 ls s3://<bucket> --no-sign-request` rather than reporting the bare listing.

### Phase 6 — Triage, nesting, and handoff

Goal: turn the flat hit lists into findings and into inputs for deeper tools.

- Re-run dir mode against every discovered directory (manual recursion) and every new vhost/subdomain from Phases 1–2.
- Hand the URL list to content-aware tooling: ffuf for parameter/value fuzzing and response-content matching, nuclei for templated vuln checks, a browser/Burp for the interesting 200s and 401/403s (a 403 directory is still a lead — try method/case/encoding bypasses).
- Keep the `-o` output files; they are your evidence trail and your dedupe record across hosts.

### Worked example — one in-scope host, four modes

A compact pass that chains the modes, all against TEST-NET placeholders:

```
# 1. Widen the host list (passive first with subfinder, then gobuster for the brute tail)
gobuster dns -do example.com -w subdomains-top1million-5000.txt -c -t 20 -o dns.txt
#   -> Found: api.example.com | Found: legacy.example.com (CNAME: oldapp.herokudns.com)
#      legacy.* CNAME behind a 404 -> subdomain-takeover lead, park it for verification

# 2. On the shared front-end IP, hunt vhosts DNS didn't show
gobuster vhost -u https://198.51.100.10 --domain example.com --append-domain \
  -w subdomains-top1million-5000.txt --exclude-status 404 --exclude-length 0 -k -o vhost.txt
#   -> Found: staging.example.com [Size: 8120]  (front end default was 404/0)

# 3. Content-discover the newly found staging host at a polite rate
gobuster dir -u https://staging.example.com -w raft-small-words.txt -t 5 -k -o dir-staging.txt
#   -> /.git (Status: 301) | /backup (Status: 301) | /api (Status: 301)

# 4. Backup/secret pass on each directory hit
gobuster dir -u https://staging.example.com/backup -w raft-small-files.txt \
  -x bak,zip,sql,tar.gz --discover-backup -k -o files-backup.txt
#   -> /backup/db.sql (Status: 200) [Size: 4201337]  -> confirm, redact, report
```

Each line is a decision point, not a step to automate blindly: the CNAME goes to a takeover check, the 200 `db.sql` gets a single confirming `curl` and a redacted excerpt, and nothing is downloaded in bulk.

## Where the money is with gobuster

Gobuster never reports a vulnerability — it reports an *asset*. The payout comes from the asset, so this table maps each mode/flag to the finding class it typically unlocks and how crowded that lane is on a mature program.

| Mode / flag | What it surfaces | Finding class it feeds | Typical severity | Dupe risk |
|---|---|---|---|---|
| `dir` + common wordlist | Generic dirs/files (`/admin`, `/login`, `/api`) | Access-control probes, info disclosure | Low–Medium | **Very high** — everyone runs this first |
| `dir -x` + `--discover-backup` | Backup/source files (`.bak`, `.old`, `.zip`, `.sql`) | Source & secret disclosure | Medium–High | Low–Medium |
| `dir` on `/.git/ /.env /.svn/` | Exposed VCS / config / secrets | Credential & source leak | High | Low |
| `dir` on `/api/` prefixes | Undocumented API routes | BOLA/BFLA surface for later testing | Medium–High (indirect) | Medium |
| `vhost --append-domain` | Forgotten/internal vhosts on a shared IP | Staging/admin access, internal apps | Medium–High | Low — under-hunted |
| `dns --check-cname` | Subdomains + CNAME targets | Subdomain takeover, surface expansion | Medium–High (takeover) | Medium |
| `s3` / `gcs` | Public/writable cloud buckets | Data exposure, bucket write | Medium–Critical | Low–Medium |

The pattern: the crowded lane (plain `dir` with a stock wordlist) is where duplicates live; the money is in the lanes that need a little more thought — backup discovery, vhosts, CNAME-driven takeovers, and buckets named from the target's own conventions. A target-specific wordlist built from the site's own paths, JS files, and naming scheme out-earns a bigger generic list almost every time.

## Command recipes

Annotated command → expected-output blocks. Low-volume settings are called out; scale threads up only with explicit rate headroom in the program policy.

```
# Rate-disciplined content sweep (≈1 req/s), logged to file
gobuster dir -u https://example.com \
  -w /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt \
  -t 1 --delay 1s -k -o dir-example.txt
```
Expected: a banner (unless `-q`), a live progress counter on stderr, and hits as `/path (Status: 301) [Size: 312]`. With `-t 1 --delay 1s` the run is slow but stays under a 1 req/s cap and barely dents the target's logs.

```
# Whitelist mode — show only "interesting" statuses, suppress the default 404 blacklist
gobuster dir -u https://example.com -w common.txt -s 200,204,301,302,307,401,403 -b "" -k
```
Expected: only the whitelisted statuses print. Note the empty `-b ""`: without it the default `-b 404` blacklist overrides your whitelist and you get unexpected results. 401/403 hits are kept deliberately — a gated path is still a discovery.

```
# Soft-404 server: filter by response length instead of status
gobuster dir -u https://example.com -w raft-medium-files.txt -b "" --exclude-length 1538 -k
```
Expected: every response is 200, so status filtering is useless; `--exclude-length 1538` drops the boilerplate page (whose body is 1538 bytes) and leaves only pages of a different size. Find the boilerplate length first with a single `curl -s https://example.com/random-xyz | wc -c`.

```
# Authenticated enumeration through Burp (captures full request/response evidence)
gobuster dir -u https://example.com -w raft-medium-directories.txt \
  -c 'session=TOKEN' -H 'X-Requested-With: XMLHttpRequest' \
  --proxy http://127.0.0.1:8080 -k -t 5
```
Expected: every request rides the session cookie and flows through Burp, so authenticated-only paths enumerate and each hit has a stored request/response pair ready for the report.

```
# Vhost discovery with noise filtering
gobuster vhost -u https://198.51.100.10 --domain example.com --append-domain \
  -w subdomains-top1million-5000.txt --exclude-status 302 --exclude-length 0 -k
```
Expected: `Found: dev.example.com Status: 200 [Size: 4102]` lines for hosts that answer differently from the front-end default; the 302 redirect boilerplate and zero-length responses are filtered out.

```
# Quick subdomain brute with CNAME capture (takeover leads)
gobuster dns -do example.com -w subdomains-top1million-5000.txt -c -t 20 -o dns-example.txt
```
Expected: `Found: assets.example.com` lines, each with a `CNAME: ...` when `-c` is set. A CNAME to a dangling cloud resource is the takeover lead; verify the target resource is unclaimed before reporting.

```
# Resume an interrupted wordlist (don't re-hit everything)
gobuster dir -u https://example.com -w big.txt --wordlist-offset 48000 -o dir-example-part2.txt
```
Expected: the scan skips the first 48,000 words and continues — use the progress counter from the killed run to pick the offset, so a timeout or disconnect doesn't cost you a restart or double the target's request load.

```
# Permutations with a pattern file ({GOBUSTER} is replaced by each word)
printf '{GOBUSTER}-dev\n{GOBUSTER}-prod\n{GOBUSTER}.bak\n' > patterns.txt
gobuster dir -u https://example.com -w names.txt -p patterns.txt -k
```
Expected: each wordlist entry is expanded through every pattern line before the request, so one list of base names yields the `-dev`/`-prod`/`.bak` variants without pre-building a huge list. `--discover-pattern`/`-pd` applies a second pattern file only to *successful* guesses — a cheap way to pivot from a hit (`admin`) into its neighbours (`admin-backup`, `admin.old`) without inflating the whole run.

```
# fuzz mode: put FUZZ anywhere — here, brute a GET parameter name
gobuster fuzz -u 'https://example.com/search?FUZZ=test' \
  -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
  --exclude-length 0 -b 404 -k
```
Expected: gobuster substitutes each word into the `FUZZ` position and reports responses that differ from the miss baseline — a parameter-name discovery pass that dir mode cannot do. `fuzz` mode is the one place gobuster accepts an arbitrary `FUZZ` keyword (in the URL, a header via `-H`, or elsewhere); it is deliberately minimal next to ffuf's matcher/filter engine, so reach for it for a quick single-position brute and switch to ffuf when you need content matching or multiple positions.

```
# mTLS-protected target: present a client certificate
gobuster dir -u https://198.51.100.10 -w raft-small-words.txt \
  --client-cert-p12 client.p12 --client-cert-p12-password 'pass' -k
```
Expected: the scan authenticates with the supplied client certificate, so an endpoint that demanded mutual TLS (and would otherwise 400/handshake-fail every request) enumerates normally. PEM pairs work too via `--client-cert-pem`/`--client-cert-pem-key` (the key must be passwordless).

```
# Pin the source interface/IP when the program allowlists a scanning egress
gobuster dir -u https://example.com -w common.txt --interface eth1 -k
# or by address:
gobuster dir -u https://example.com -w common.txt --local-ip 203.0.113.50 -k
```
Expected: requests leave from the named interface/IP. `--interface`/`-iface` and `--local-ip` are mutually exclusive. Useful when the program requires traffic to originate from a registered static IP so the owner can attribute and whitelist your scan.

### Wordlist strategy — the real lever on signal

The wordlist, not the flags, decides whether a run finds anything new. A stock `common.txt` run is almost pure duplicate on a mature program; the hits that pay are reached by lists nobody else is firing:

- **Build a target-specific list.** Crawl the app (katana/hakrawler/Burp), extract every path, directory segment, and parameter from the HTML and JS, dedupe, and feed *that* to gobuster. The app's own naming scheme predicts its hidden paths better than any generic list.
- **Mine JS for endpoints.** Front-end bundles reference API routes that are never linked in HTML. Pull strings from the JS, turn them into a wordlist, and brute the API host.
- **Escalate, don't start big.** `raft-small` → `raft-medium` → `directory-list-2.3-medium` only on hosts that returned something at the smaller tier. A `-110000` DNS list or a medium directory list on every host burns your rate budget on dupes.
- **Match the stack.** Infer the server/framework (headers, error pages) and pick extensions to suit — `asp,aspx,config` for IIS, `php,inc` for PHP, `jsp,action` for Java, `json,js,map` for SPA asset dirs — rather than requesting every extension everywhere.

## Pitfalls, noise and scope discipline

- **Default rate is aggressive.** 10 threads with no delay against a small site is a visible spike; a 100-thread (`-t 100`) run on a medium wordlist is a mini-DoS and a fast way to get your IP blocked or your report rejected for policy violation. Unless the program explicitly allows high rates, stay at `-t` single digits with a `--delay`. There is no requests-per-second flag — do the arithmetic yourself.
- **Brute force is in-scope only when the policy says so.** "No automated scanning" clauses cover gobuster. Content discovery allowed on web assets does not extend to hammering an API gateway or a login endpoint. Confirm per-host.
- **Soft-404 / catch-all servers produce all-noise output.** If everything returns 200, status filtering is meaningless and every line is a false positive until you add `--exclude-length`. Always baseline a guaranteed-miss first.
- **`--wildcard` on a wildcard DNS domain floods you with false subdomains.** The default abort is protective; only pass `--wildcard` if you have a plan to filter, or you will "find" thousands of non-existent hosts.
- **The short-flag drift (see Phase 0) silently mis-scans.** `-d` = delay, `-do` = domain; `-s` = status-codes in dir but show-files in s3. A wrong short flag can make gobuster scan nothing, or set a huge delay, without an obvious error.
- **No recursion means a flat view.** If you forget to re-run on discovered directories, you miss everything below the first level. Know the gap: gobuster is a fast flat sweep, feroxbuster/ffuf are the recursive tools.
- **Buckets: listing ≠ permission to read everything.** Finding a public bucket is fair game; downloading its full contents is not. Sample enough to prove exposure (`--max-files`, a couple of keys) and stop — exfiltrating customer data is a scope and legal violation even on an in-scope asset.
- **Don't auth-brute with it.** gobuster is for paths/hosts/buckets, not credentials. Pointing it at a login endpoint with a password list is both the wrong tool and almost certainly a policy breach.

### Where ffuf / feroxbuster beat gobuster (know the tool gap)

| Need | gobuster | Reach for instead |
|---|---|---|
| Recursive directory discovery | Not supported (manual re-run) | feroxbuster (default), `ffuf -recursion` |
| Requests-per-second cap | Only `--threads` + `--delay` | `ffuf -rate`, feroxbuster `--rate-limit` |
| Match/filter by response content (words, lines, regex) | Status + length only | `ffuf -mr/-fr`, `-mw/-fw`, `-ml/-fl` |
| Multiple FUZZ positions / parameter fuzzing | `fuzz` mode, one `FUZZ` keyword | ffuf (clusterbomb/pitchfork) |
| Auto-calibration of soft-404s | Manual `--exclude-length` | `ffuf -ac` |

gobuster's edge is the opposite of these: fast startup, low memory, sane defaults, and first-class `vhost`, `dns`, and cloud-bucket modes in one binary. Use it for the fast flat sweep and the vhost/bucket/subdomain work; switch to ffuf/feroxbuster when you need recursion, a true rate limit, or content-based matching.

## Reporting evidence

Gobuster findings are discoveries, so the evidence is "this asset exists and is reachable, here is proof" — the exploit write-up comes from whatever you did with it next. Capture:

- **The exact command**, including wordlist path and version. Run `gobuster version` and record it; flag semantics (notably `--append-domain`, the status-code override, short-flag meanings) have drifted across releases, so the version makes your command reproducible.
- **The `-o` output file**, unedited — it is the ordered, timestamped record of what was requested and what came back. Keep one per host/mode.
- **A confirming single request per reported hit.** A gobuster line proves the brute saw it; a clean `curl -i` (or Burp request/response pair) proves it to a triager without them re-running your scan. For a found path: `curl -i https://example.com/.git/config`. For a vhost: `curl -i https://198.51.100.10/ -H 'Host: staging.example.com'`. For a bucket: `aws s3 ls s3://bucket --no-sign-request`.
- **The response that makes it a finding** — the body of the `.env`/`.git/config`/backup (redact secrets to the minimum needed to prove exposure), the distinct status/size of the hidden vhost, the bucket's object listing. Never dump more than the proof requires, and clean up anything you created.
- **The baseline for contrast.** Include the guaranteed-miss response (random path → 404/soft-404 size) so the triager sees why your hit is real and not catch-all noise.
- **For takeover leads**, the CNAME chain (`dig CNAME sub.example.com`) plus the "unclaimed" signal from the pointed-to provider — not just the gobuster `CNAME:` line.

Frame impact as the asset's consequence, not the discovery itself: "world-readable S3 bucket `example-backups` containing database dumps" beats "found a bucket"; "exposed `.git` directory allows full source reconstruction (`git-dumper`)" beats "`/.git/` returned 200".

### Sample finding skeleton

```
Title: Exposed .git directory on staging.example.com permits full source reconstruction

Asset:  https://staging.example.com/.git/  (found via vhost + dir discovery)
Severity: High (source + likely secret disclosure)

Discovery (reproducible):
  gobuster version        # v3.x.x
  gobuster vhost -u https://198.51.100.10 --domain example.com --append-domain \
    -w subdomains-top1million-5000.txt --exclude-status 404 -k
    -> Found: staging.example.com [Size: 8120]
  gobuster dir -u https://staging.example.com -w raft-small-words.txt -t 5 -k
    -> /.git (Status: 301) [Size: 178]

Confirmation (single request):
  $ curl -is https://staging.example.com/.git/config
    HTTP/1.1 200 OK
    [Core]  repositoryformatversion = 0 ...
    [remote "origin"] url = git@github.com:example/staging.git   <-- redact as needed

Impact: the .git tree is reconstructable, exposing application source and any
  committed secrets. Baseline miss (/random-xyz) returns 404 [Size: 178-same], so
  the 200 on /.git/config is not catch-all noise.

Remediation: block dotfiles/VCS dirs at the web server; purge committed secrets
  and rotate them.
```

Attach the unedited `-o` output files and the baseline-miss response alongside; that is the full evidence set a triager needs to reproduce without re-running your scan.

## Flag quick reference (verified against master source)

Every flag below was read from the urfave/cli v2 registrations in the master-branch source (`cli/options.go`, `cli/dir/dir.go`, `cli/dns/dns.go`, `cli/vhost/vhost.go`, `cli/s3/s3.go`, `cli/gcs/gcs.go`). Short forms are the real aliases — note they are often two letters, and they drift between modes.

**Global (all modes)**

| Flag | Short | Default | Purpose |
|---|---|---|---|
| `--wordlist` | `-w` | — (required) | Wordlist path; `-` for STDIN |
| `--threads` | `-t` | 10 | Concurrent threads |
| `--delay` | `-d` | none | Per-thread wait between requests |
| `--wordlist-offset` | `-wo` | 0 | Resume from a wordlist position |
| `--output` | `-o` | stdout | Write results to a file |
| `--quiet` | `-q` | false | Suppress banner/noise |
| `--no-progress` | `-np` | false | Suppress the progress line |
| `--no-error` | `-ne` | false | Suppress error lines |
| `--no-color` | `-nc` | false | Disable colour |
| `--pattern` | `-p` | — | Pattern file (`{GOBUSTER}` replacement) |
| `--discover-pattern` | `-pd` | — | Pattern file applied to successful guesses only |
| `--debug` | — | false | Debug output |

**HTTP flags (dir, vhost; basic set also in s3/gcs)**

| Flag | Short | Default | Purpose |
|---|---|---|---|
| `--url` | `-u` | — (required) | Target URL |
| `--cookies` | `-c` | — | Request cookies |
| `--headers` | `-H` | — | Extra headers (repeatable) |
| `--username` / `--password` | `-U` / `-P` | — | Basic-auth creds |
| `--method` | `-m` | GET | HTTP method |
| `--follow-redirect` | `-r` | false | Follow redirects |
| `--useragent` | `-a` | gobuster default | User-Agent string |
| `--random-agent` | `-rua` | false | Random User-Agent |
| `--proxy` | — | — | `http(s)://` or `socks5://` proxy |
| `--timeout` | `-to` | 10s | HTTP timeout |
| `--no-tls-validation` | `-k` | false | Skip TLS cert checks |
| `--retry` / `--retry-attempts` | — / `-ra` | false / 3 | Retry on timeout, N times |
| `--client-cert-p12` / `...-password` | `-ccp12` / `-ccp12p` | — | mTLS via p12 |
| `--client-cert-pem` / `...-key` | `-ccp` / `-ccpk` | — | mTLS via PEM pair (key passwordless) |
| `--interface` / `--local-ip` | `-iface` / — | — | Egress interface / source IP (mutually exclusive) |

**dir mode**

| Flag | Short | Default | Purpose |
|---|---|---|---|
| `--status-codes` | `-s` | — | Whitelist statuses (ranges ok) |
| `--status-codes-blacklist` | `-b` | 404 | Blacklist statuses; overrides `-s` if set |
| `--extensions` / `--extensions-file` | `-x` / `-X` | — | Extensions to append (inline / from file) |
| `--exclude-length` | `-xl` | — | Drop responses by body length (ranges ok) |
| `--add-slash` | `-f` | false | Append `/` to each request |
| `--discover-backup` | `-db` | false | On a file hit, probe backup extensions |
| `--expanded` | `-e` | false | Print full URLs |
| `--no-status` / `--hide-length` | `-n` / `-hl` | false | Trim output columns |
| `--force` | — | false | Continue past precheck failures |

**dns mode**

| Flag | Short | Default | Purpose |
|---|---|---|---|
| `--domain` | `-do` | — (required) | Target domain (**not `-d`**) |
| `--check-cname` | `-c` | false | Also report CNAME targets |
| `--wildcard` | `-wc` | false | Continue when a wildcard record is found |
| `--no-fqdn` | `-nf` | false | Don't add trailing dot (use search domains) |
| `--resolver` | — | system | Custom DNS server (`host` or `host:port`) |
| `--protocol` | — | udp | `udp` or `tcp` (tcp needs `--resolver`) |
| `--timeout` | `-to` | 1s | Resolver timeout |

**vhost mode**

| Flag | Short | Default | Purpose |
|---|---|---|---|
| `--append-domain` | `-ad` | false | Append base domain to each word |
| `--domain` | `-do` | — | Domain to append when `-u` is an IP |
| `--exclude-length` | `-xl` | — | Drop responses by length |
| `--exclude-status` | `-xs` | — | Drop responses by status (ranges ok) |
| `--exclude-hostname-length` | `-xh` | false | Auto-adjust exclude-length per response |
| `--force` | — | false | Force past the plain-http-via-proxy guard |

**s3 / gcs modes**

| Flag | Short | Default | Purpose |
|---|---|---|---|
| `--max-files` | `-m` | 5 | Max object keys to list per bucket |
| `--show-files` | `-s` | true | Print the object keys |

(The wordlist supplies bucket-name candidates; these modes take the global and basic-HTTP flags but not the cookie/auth set.)

## References

- Gobuster repository and README (OJ/gobuster) — https://github.com/OJ/gobuster
- Flag definitions verified against master source: `cli/options.go` (global, common-HTTP, basic-HTTP flag sets), `cli/dir/dir.go`, `cli/dns/dns.go`, `cli/vhost/vhost.go`, `cli/s3/s3.go`, `cli/gcs/gcs.go` — https://github.com/OJ/gobuster/tree/master/cli
- Recursion absence confirmed in `gobusterdir/gobusterdir.go` — https://github.com/OJ/gobuster/blob/master/gobusterdir/gobusterdir.go
- SecLists wordlists (Discovery/DNS, Discovery/Web-Content) — https://github.com/danielmiessler/SecLists
- ffuf (recursion, rate limiting, content matchers) — https://github.com/ffuf/ffuf
- feroxbuster (recursive by default, `--rate-limit`) — https://github.com/epi052/feroxbuster

*Research supplement added 2026-11-16, grounded in the OJ/gobuster README and the master-branch flag source under `cli/` and `gobusterdir/` (verified against urfave/cli v2 flag registrations; no flag cited that is absent from the source) (model: claude-opus-4-8).*
