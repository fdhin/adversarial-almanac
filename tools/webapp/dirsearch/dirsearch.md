---
name: "dirsearch"
categories:
  - blackarch-webapp
version: "3033.b45e01bb"
homepage: "https://github.com/maurosoria/dirsearch"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "maurosoria/dirsearch"
github-stars: 15052
github-pushed: "2026-11-05"
github-archived: false
github-issues: 22
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 510
gh-activity-checked: "2026-11-09"
---

# dirsearch

> Advanced command-line brute-forcer for discovering web paths and files.

## What it's for

dirsearch enumerates a web server's content by brute-forcing directory and file paths from wordlists, optionally appending extensions to every word and recursing into directories it finds. It is the classic first-pass tool for web content discovery: point it at a target with a few extensions and read off the interesting paths (admin panels, backups, forgotten scripts). It only reports what responds — it is not a vulnerability scanner. Runs on Python 3.11+ and also exposes an importable Python API for automation.

## Typical use cases

### First-pass content discovery with extensions

```
dirsearch -u https://example.com -e php,html,js
```

Outcome: brute-forces the site root with dirsearch's bundled wordlist, appending each listed extension to every word, and prints discovered paths with status code and size — the quick map of what exists that drives the next enumeration step.

### Scan with an external wordlist

```
dirsearch -u https://target.example.com -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-small.txt
```

Outcome: replaces the default wordlist with a SecLists file and reports found paths live as they respond; the form to use when the bundled list is exhausted or a target-specific list fits better.

### Recurse into found directories

```
dirsearch -u https://target.example.com -e php -r --max-recursion-depth 3
```

Outcome: after each discovered directory, keeps brute-forcing inside it down to depth 3 (0 means unlimited), catching content nested below the root instead of stopping at the first level.

### Keep only interesting status codes

```
dirsearch -u https://target.example.com -i 200,301,302,401,403
```

Outcome: only paths returning one of the listed codes are reported, dropping 404 noise and letting a big scan converge on what matters.

### Save the scan report

```
dirsearch -u https://target.example.com -e php -o dirsearch-report.txt
```

Outcome: writes the full scan report to the named file in addition to the console output, leaving a record to diff against later scans.

_Use cases grounded in corpus evidence (the `-u`/`-e` and `-w` patterns dominate real usage) and verified against the upstream options reference (recursion, status filters, reports)._

---

## Bug bounty workflow with dirsearch

dirsearch is a traffic generator. Every word it tries is a request against the target, and its two force multipliers — extensions and recursion — multiply that traffic geometrically. On an authorized program the whole game is reaching the high-value paths (admin panels, backups, `.git`) while staying under the program's rate rules and never stepping outside scope. The workflow below is ordered so that cheap, low-volume passes find the obvious wins first, and the expensive wide/deep passes only run once you have fingerprinted the stack and know they are worth the traffic.

**Ground rules that apply to every phase.** Content discovery is active and noisy by nature — there is no passive mode. So: confirm the host is in scope *before* the first request (an in-scope apex rarely authorizes `*.`-subdomain brute forcing — enumerate hosts with [[subfinder]]/[[amass]] and test only the ones the program lists); keep threads low on production (`-t 5`–`10`, not the default 25); cap the request rate with `--max-rate`; and never let an unbounded recursive scan with a 220k-word list run against a single-host program. dirsearch's own docs warn that high thread counts "can cause denial of service" — treat that as a scope-violation risk, not just a performance note.

### Phase 0 — Scope, authorization, and fingerprint

Goal: know exactly which hosts you may hit and what tech stack they run, so you fold only the extensions that matter and never brute a host outside the program.

```bash
# What stack is this? Header + a couple of known paths decide your extension set.
curl -sI https://example.com/ | grep -iE 'server|x-powered-by|x-aspnet-version|set-cookie'
# PHPSESSID -> php ;  ASP.NET_SessionId / X-AspNet-Version -> aspx,asp,ashx ;
# JSESSIONID -> jsp ;  nginx/none -> stay generic, lean on backup/config extensions
```

The fingerprint decides your `-e` list (see extension-folding below) and whether a path like `/admin.php` is even plausible. Record the `Server` header and one known-good 200 response — they are your calibration baseline for Phase 1. If the program authorizes only `example.com` and `www.example.com`, those are the only two `-u` targets; a wildcard scope still does not license a `*.`-style sweep unless the program says so in writing.

### Phase 1 — Baseline a single low-volume pass with calibration

Goal: a quick map of the root with the **bundled** list, low threads, rate-capped, so dirsearch's wildcard/soft-404 calibration settles before you commit to a big list.

```bash
dirsearch -u https://example.com -e php -t 8 --max-rate 20 -i 200,204,301,302,307,401,403
```

dirsearch automatically calibrates against wildcard and soft-404 responses (a server that answers `200` to everything), so you normally do not need to tune it; `--auto-calibration` forces extra calibration samples from the start when a target is especially noisy. Read the first screen of hits, not the whole run — if the root is already dense with real paths, you may not need a bigger list at all.

### Phase 2 — Triage the hits into a candidate ladder

Goal: separate the reportable classes (admin panels, backups, VCS, config, keys) from the noise, before spending traffic deepening anything.

Status filtering is your first knife. `-i 200,301,401,403` keeps only interesting codes; `-x 404,429,500-599` drops noise and server-error floods. Both accept ranges (`-i 200,300-399`). When a target returns a near-constant soft response you cannot calibrate away, switch to advanced filters by response **size/words/lines**: `--exclude-sizes 0,1337B`, or `--fs`/`--fw`/`--fl` (advanced filter) and `--mc`/`--ms` (advanced matcher) to pin exactly the fingerprint of the boilerplate page. A directory that returns `401`/`403` is not a dead end — it is a *confirmed-exists* signal and goes to Phase 6.

How to read what dirsearch reports, and what each code is worth to triage next:

| Code | Means | Next move |
|---|---|---|
| `200` | Path served content | Hand-verify (soft-404?), then assess the body — the finding candidates live here |
| `204` | Exists, empty body | Often an API/endpoint stub — note and probe with other verbs |
| `301`/`302`/`307` | Redirect | Read the `Location` (`--redirects-history`, `-F` to follow) — frequently `/admin` → `/admin/login` confirms existence |
| `401` | Auth required | Confirmed-exists; retry with Phase 5 credentials |
| `403` | Forbidden | Confirmed-exists; Phase 6 bypass attempt (manual) |
| `405` | Method not allowed | Path exists; try `-m POST`/`PUT` — a verb gap can expose it |
| `429` | Rate-limited | You are over the line — `--skip-on-status 429`, lower `--max-rate` |
| `500-599` | Server error | `-x 500-599` to drop; but a path that *only you* can 500 is worth a manual look |

When calibration cannot kill a soft-404 by size alone, hand dirsearch a sample of the soft page and let it match structurally: `--exclude-response 404.html` (save the soft-404 body first, then exclude responses similar to it) is more robust than guessing a byte size, and `--min-response-size`/`--max-response-size` bracket the plausible band for real content.

### Phase 3 — Deepen deliberately: bigger lists, extensions, backups

Goal: only now spend the traffic on wider wordlists, folded extensions, backup hunting, and (carefully) recursion — each with the math done first.

**Wordlist escalation ladder** (small→large, stop when returns dry up):

| List | Rough size | When |
|---|---|---|
| bundled `db/dicc.txt` (default) | few thousand | first pass, every target |
| `--wordlist-categories common,conf,backups,vcs,keys` | targeted | hunt specific finding classes without a giant generic list |
| SecLists `Discovery/Web-Content/common.txt` | ~4.7k | quick generic top-up |
| SecLists `raft-large-directories.txt` / `-files.txt` | ~62k / ~37k | deep dir/file sweep on a tolerant target |
| SecLists `directory-list-2.3-medium.txt` | ~220k | last resort; huge traffic — only with explicit headroom |

Size vs coverage is a real tradeoff: `directory-list-2.3-medium` finds more but is ~45× the traffic of `common.txt`, and on a bounty program that 220k-request burst is far more likely to trip WAF/rate limits (and get you a scope-violation complaint) than to find something the 62k `raft` list missed. Escalate one rung at a time.

**Backups and VCS** are the single highest-value-per-request add-ons:

```bash
# Discovered files only: look for .bak/.old/.save/~/.swp siblings of each hit
dirsearch -u https://example.com -e php -t 8 --max-rate 20 --find-backup
# Category-targeted sweep for config, VCS, keys, logs without a generic list
dirsearch -u https://example.com --wordlist-categories backups,conf,vcs,keys,logs -t 8 --max-rate 20
```

`--find-backup` asks dirsearch to look for backups of the files it already found — a tight, high-signal add that turns `app.php` into a test for `app.php.bak`, `app.php~`, `app.php.old`. The `vcs` category chases `.git/`, `.svn/`, `.hg/`; a `.git/HEAD` or `.git/config` hit is a top-tier finding (Phase 7).

### Phase 4 — Recursion, with the traffic math done first

Recursion is where hunters accidentally DoS a target. `-r` re-runs the **entire wordlist** inside every directory it finds; `-R N` caps depth (`-R 0` is unlimited — never use that on a program). Do the multiplication before you launch:

> Let **B** = baseline requests for one pass (≈ wordlist words × (1 + extensions)). Suppose B ≈ 50,000 and the root reveals **D ≈ 30** directories.
> - Depth 1 (`-R 1`): B × (1 + D) ≈ 50,000 × 31 ≈ **1.55 M requests**.
> - Depth 2 (`-R 2`): B × (1 + D + D²) ≈ 50,000 × (1 + 30 + 900) ≈ **46.5 M requests**.
> - Depth 3 compounds again — millions become hundreds of millions.

That is why recursion runs **last**, on a **small** list, with **shallow** depth, scoped to directories worth descending:

```bash
# Shallow, status-gated recursion on a trimmed list — not the 220k monster
dirsearch -u https://example.com/app/ -w SecLists/.../common.txt -e php \
  -r -R 2 --recursion-status 200,301,401,403 -t 8 --max-rate 20 \
  --exclude-subdirs assets,static,images,fonts,node_modules
```

`--recursion-status` only descends into directories that returned an interesting code (don't recurse a soft-404 tree); `--exclude-subdirs` prunes asset dumps that waste the entire budget; `--deep-recursive` and `--force-recursive` exist but *increase* traffic and are rarely justified on a bounty target.

### Phase 5 — Authenticated discovery

Goal: the logged-in surface is where the interesting app lives, and it is invisible to an anonymous scan.

```bash
# Cookie-authenticated area
dirsearch -u https://example.com/dashboard/ -e php --cookie "session=<token>" -t 8 --max-rate 20
# Header / bearer token
dirsearch -u https://api.example.com/ -H "Authorization: Bearer <token>" -t 8 --max-rate 20
# Built-in auth helpers
dirsearch -u https://example.com/ --auth user:pass --auth-type basic   # also: digest, bearer, ntlm, jwt
```

Scope the authenticated scan to the paths your role should reach; discovering an admin-only path *with a low-privilege session* that then serves content is a BFLA/authorization finding, not just a discovery. Pair found admin paths with the two-account test from your authorization methodology.

### Phase 6 — Turn a 403 into a finding (manual, low volume)

A `403` on `/admin/` means the path exists and something is guarding it. dirsearch's job ends at discovery; the bypass is a **manual, single-request** follow-up — never a scripted flood. Try, one request at a time:

```bash
# Path-normalization tricks
curl -sI --path-as-is https://example.com/admin/        # trailing slash
curl -sI --path-as-is https://example.com/admin/.       # /./
curl -sI --path-as-is https://example.com/admin//       # double slash
curl -sI --path-as-is 'https://example.com/admin/..;/'  # path param
curl -sI https://example.com/%61dmin/                    # percent-encoded 'a'
curl -sI https://example.com/ADMIN/                      # case
# Header-based access-control bypass
curl -sI https://example.com/admin/ -H 'X-Original-URL: /admin/'
curl -sI https://example.com/admin/ -H 'X-Rewrite-URL: /admin/'
curl -sI https://example.com/admin/ -H 'X-Forwarded-For: 127.0.0.1'
curl -sI https://example.com/admin/ -H 'X-Custom-IP-Authorization: 127.0.0.1'
```

Any variant that flips `403`→`200` and serves the guarded content is a reportable access-control bypass. Keep it to a handful of manual requests and capture the exact request/response pair; do not run a bypass wordlist at scale against a production program.

### Phase 7 — Validate and cross-check

Goal: confirm the finding is real and not a calibration artifact, and escalate the high-value classes.

- **Re-request the hit by hand.** A path that dirsearch flagged `200` but a plain `curl` shows as a soft-404/redirect was a calibration miss — drop it. Use `--full-url` and `--redirects-history` in the scan so you can see exactly what responded.
- **Exposed `.git/` → confirm and scope with [[nuclei]].** When the `vcs` category or a `.git/config`/`.git/HEAD` hit lands, cross-check with [[nuclei]]'s git-exposure templates (`http/exposures/configs/git-config.yaml` and the `.git/` detection set) to confirm the repository is actually dumpable before you attempt a `git-dumper` reconstruction. Report the exposure; only reconstruct enough to prove impact.
- **Backups/config/keys → open and prove, minimally.** A `.bak` or `.env` hit is proven by the *first few bytes* showing it is the real file, not by exfiltrating the whole thing.

---

## Where the money is with dirsearch

| Priority | Finding class | How dirsearch reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Exposed VCS (`.git/`, `.svn/`, `.hg/`) | `--wordlist-categories vcs`; confirm with [[nuclei]] | High–Critical (source/secrets) | Medium — scanners find it fast; be first/thorough |
| 2 | Backup/source archives (`.bak`, `.old`, `.zip`, `.sql`, `~`, `.swp`) | `--find-backup`, `--wordlist-categories backups` | High–Critical | Medium–High |
| 3 | Secrets/config files (`.env`, `config.php.bak`, `web.config`, `.aws/credentials`) | `--wordlist-categories conf,keys` | High–Critical | Medium |
| 4 | Admin / internal panels behind weak or no auth | default list + `-e`; then auth test (Phase 5) | High (if reachable) | High — very commonly reported |
| 5 | 403-guarded paths bypassable by header/path tricks | discovery + manual Phase 6 | Medium–High | Medium |
| 6 | Directory listing / index exposure | `-i 200` on directory paths; eyeball body | Low–Medium | High |
| 7 | Debug/status/monitoring endpoints (`/actuator`, `/server-status`, `/debug`) | `--wordlist-categories common` + targeted words | Medium | Medium |
| 8 | Old/forgotten app versions, staging copies | large list + recursion (Phase 4) | varies | Low — unique to target |
| 9 | API route/version discovery (`/api/v1`, `/swagger`, `/openapi.json`) | targeted wordlist; feed results to API testing | Info→High (enables other bugs) | High |

The top three rows are where dirsearch out-earns a generic scanner, because `--find-backup` and the `backups`/`vcs`/`keys` categories are purpose-built for them and most hunters only run a generic directory list. Rows 4 and 6 are heavily dupe-prone — a found admin panel is only a payout if you can show it is *reachable* or *bypassable*, not merely that it exists.

---

## Command recipes

**First-pass, production-safe default.** Low threads, rate-capped, interesting codes only.

```bash
dirsearch -u https://example.com -e php,html -t 8 --max-rate 20 -i 200,301,401,403 -o run1.txt
```
Expected: a short list of live paths with `status - size - path`, written to `run1.txt`. This is the pass you run on every target first.

**Stack-correct extension folding.** Fold only the extensions the fingerprint justifies — each extension multiplies the request count.

```bash
# PHP target
dirsearch -u https://example.com -e php,phtml,bak,old,zip,sql -t 8 --max-rate 20
# IIS / .NET target
dirsearch -u https://example.com -e aspx,asp,ashx,asmx,config,bak -t 8 --max-rate 20
```
`-e` replaces the `%EXT%` keyword in a `%EXT%`-style list (dicc.txt), generating `index`, `index.php`, `index.phtml` from `index.%EXT%`. Folding `php` *and* `aspx` *and* `jsp` on one host triples your traffic to test extensions that cannot all exist — don't shotgun; pick the stack.

Extension strategy, by fingerprint — fold the app's language extensions plus the universal backup set, and skip the rest:

| Stack signal | App extensions to fold (`-e`) | Why |
|---|---|---|
| `PHPSESSID`, `X-Powered-By: PHP` | `php,phtml,php3,php5,inc` | PHP executes these; `.inc` often serves source |
| `ASP.NET_SessionId`, `X-AspNet-Version` | `aspx,asp,ashx,asmx,config` | `web.config` is a secret-bearing config file |
| `JSESSIONID`, servlet headers | `jsp,jspa,do,action,action` | Java web stacks |
| Node/nginx, no language tell | `js,json,map` (plus backups) | `.map` leaks source; stay light on exec extensions |
| **Universal (every target)** | `bak,old,save,swp,zip,tar.gz,sql,txt,log,env` | Backup/source/secret classes — highest value per request |

The universal backup/config set is where the payouts are, so even on an unknown stack lead with those rather than guessing language extensions. Each added extension multiplies B (baseline requests); a 6-extension fold is 6× the language-less traffic, so keep the list tight and let `--find-backup` handle per-file backup variants instead of folding ten backup extensions onto every word.

**SecLists (no `%EXT%`) needs force-extensions.** SecLists directory lists are bare words with no `%EXT%` token, so `-e` alone does nothing useful — use `-f`/`--force-extensions` to append extensions (and `/`) to every entry.

```bash
dirsearch -u https://example.com \
  -w SecLists/Discovery/Web-Content/raft-large-directories.txt \
  -e php,bak -f -t 8 --max-rate 20
```
Expected: `admin` yields requests for `admin`, `admin.php`, `admin.bak`, and `admin/`. Without `-f`, a SecLists scan with `-e` just tries the bare words.

**Overwrite existing extensions.** When the list already carries extensions and you want to swap them (e.g. a list of `.html` paths on a `.jsp` app):

```bash
dirsearch -u https://example.com -w pages-with-ext.txt -e jsp,jspa --overwrite-extensions
```
`login.html` → `login.html`, `login.jsp`, `login.jspa`. Note dirsearch deliberately won't overwrite data-ish extensions like `.log`, `.json`, `.xml`, `.jpg`, `.png`.

**Backup hunt on discovered files.**

```bash
dirsearch -u https://example.com -e php -t 8 --max-rate 20 --find-backup -O json -o backups.json
```
`-O`/`--output-formats` accepts `simple,plain,json,xml,md,csv,html,sqlite`; JSON/JSONL is the machine-readable form to diff between runs or feed to other tools.

**Combine matchers for a noisy target (AND/OR logic).** When no single filter cleans the output, compose them:

```bash
# Keep only 200s whose body is NOT the 1234-byte boilerplate AND is >500 bytes
dirsearch -u https://example.com -e php --mc 200 --fs 1234 --min-response-size 500 \
  --fmode and -t 8 --max-rate 20
```
`--mmode`/`--fmode` set the matcher/filter operator (`and`/`or`) across the `--mc/--ms/--mw/--ml/--mr` matchers and `--fc/--fs/--fw/--fl/--fr` filters, so you can require several conditions at once instead of one blunt status filter. This is the precise instrument for a server that soft-404s with varying sizes.

**Verb testing on a discovered path.** A path that is `403`/`405` on GET may answer a different method:

```bash
dirsearch -u https://example.com/api/ -w SecLists/.../api/endpoints.txt -m POST -t 5 --max-rate 15
dirsearch -u https://example.com/admin/ -m PUT -t 5 --max-rate 10        # verb-gap probe
```
`-m`/`--http-method` sets the method (default GET). Pair with `-d`/`--data` or `--data-file` when the endpoint needs a body. A route guarded on GET but open on PUT/POST is a function-level authorization finding, not merely discovery.

**Replay hits through Burp for inspection.**

```bash
dirsearch -u https://example.com -e php -t 5 --max-rate 10 --replay-proxy http://127.0.0.1:8080
```
`--replay-proxy` sends only the *found* paths back through your proxy, so Burp's history holds the full request/response of each hit for evidence — without proxying the entire noisy scan.

**Authenticated, rate-disciplined scan of a URL list.**

```bash
dirsearch -l authorized-hosts.txt -e php -t 5 --max-rate 15 \
  --cookie "session=<token>" --skip-on-status 429 -O csv -o scoped.csv
```
`-l` reads targets from a file (every line must be in scope); `--skip-on-status 429` abandons a target the moment it starts rate-limiting you, which both respects the server and stops you from burning the run on `429` noise.

**Load an exact request from Burp.** Reproduce a complex authenticated/multiheader request precisely:

```bash
dirsearch --raw request.txt --scheme https -e php -t 5 --max-rate 15
```
`--raw` loads a saved raw HTTP request (headers, cookies, method, body) and fuzzes paths within it; `--scheme` sets https since the raw file has no scheme.

**Preview the generated wordlist size before committing traffic.**

```bash
dirsearch -u https://example.com -e php,bak,old -w big.txt --wordlist-status
```
`--wordlist-status` resolves the files and prints the generated entry count, then exits — run it before any large/recursive scan so you know B (the baseline request count) and can do the recursion math from Phase 4. `--wordlist-max-size` (default 500000) aborts if generation explodes past a ceiling.

**Crawl discovered pages for new paths.** Mine the HTML/JS of pages dirsearch already found for links the wordlist never contained:

```bash
dirsearch -u https://example.com -e php -t 8 --max-rate 20 --crawl
```
`--crawl` ("crawl for new paths in responses") turns real app content into target-specific paths — far higher signal than a generic list, and it catches endpoints no dictionary would guess.

**Pause and resume a long authorized scan.** Large scoped scans should be resumable so a disconnect doesn't force a full re-run (and re-traffic):

```bash
dirsearch -u https://example.com -w raft-large-directories.txt -e php -t 8 --max-rate 20 -s scan.session
# ...interrupted; later:
dirsearch --list-sessions          # see resumable sessions and ids
dirsearch -s scan.session          # resume exactly where it stopped
```
Resuming avoids re-sending every already-tried request — both polite to the target and faster for you.

**Capture full response bodies for evidence as you scan.**

```bash
dirsearch -u https://example.com -e php,bak -t 5 --max-rate 15 \
  --save-response ./dirsearch-responses --save-response-jsonl hits.jsonl
```
`--save-response` writes each matched response body to a directory and `--save-response-jsonl` appends matched responses to a JSONL file — so the evidence for a backup/config hit is captured at discovery time, not reconstructed later. Keep these local and redact before any report.

**Scope-bounded CIDR / nmap-driven discovery (authorized ranges only).** For an authorized network range, feed targets from an nmap service scan rather than guessing hosts:

```bash
# nmap first (service detection), then dirsearch only the web services it found
dirsearch --nmap-report nmap-sV.xml -e php -t 5 --max-rate 15 --timeout 3 --retries 1
# or an explicit in-scope documentation range
dirsearch --cidr 198.51.100.0/24 -e php -t 5 --max-rate 10 --skip-on-status 429
```
`--nmap-report` loads targets from an nmap run (use `-sV` so dirsearch sees the web ports); `--cidr` scans a range. Both demand that *every* resulting host is inside the authorized scope — a `--cidr` that spills past the program boundary is a violation no matter what it finds. `--timeout 3 --retries 1` keeps a wide scan from stalling on dead hosts.

---

## Pitfalls, noise and scope discipline

**Threads at the default 25 on production is the classic ban.** The default is tuned for lab throughput, not for a live program. On a bounty target drop to `-t 5`–`10` and add `--max-rate` (requests/second; `0` = unlimited — never leave it unlimited on a program). A rate-capped, low-thread scan is slower but is the difference between a finding and a scope-violation report against *you*.

**Unlimited or deep recursion is a self-inflicted DoS.** `-r` without `-R` (or `-R 0`) re-runs the whole wordlist in every directory at every depth; with a large list this is tens of millions of requests (Phase 4 math). Always set a shallow `-R`, gate with `--recursion-status`, and prune with `--exclude-subdirs`. If you would not send that request count by hand, dirsearch should not send it either.

**Scope creep via wordlists and wildcards.** dirsearch brute-forces *paths*, not subdomains — but feeding it a `-l` list that includes out-of-scope hosts, a `--cidr` range that spans assets you were not authorized for, or a target the program never listed is a real violation. Verify every host in a `-l` file and every address in a `--cidr` against the program scope first. `*.`-style subdomain sweeping is a separate activity ([[subfinder]]/[[amass]]) and is only in scope if the program explicitly allows it.

**Soft-404 / wildcard false positives.** A server that returns `200` for everything will make dirsearch report the entire wordlist as "found." dirsearch auto-calibrates, but when results look impossibly dense, re-run with `--auto-calibration`, or pin the boilerplate with size/word filters (`--exclude-sizes`, `--fs`, `--exclude-text`). Always hand-verify a sample of hits with `curl` before reporting — a calibration artifact in a report is an instant credibility hit with triagers.

**429 floods and WAF tripping.** If you see a wall of `429`, you are already over the line — `--skip-on-status 429` stops hammering, lower `--max-rate`, add `--delay`, and consider `--random-agent`. Do **not** reach for `--tor`/`--proxies-file` rotation to evade a rate limit on a bounty target; defeating the program's own rate controls is itself a violation and reads as malicious. Proxy rotation is a tool for distributed-but-authorized testing, not for dodging defenses.

**Aggressive category = WAF bait.** The bundled `aggressive` category (and `--wordlist-categories all`, which includes it) carries "payloads likely to trigger WAF rules." Only use it when the program tolerates noisy scanning; it is excluded from the default `db/dicc.txt` and the `common` category for exactly this reason.

**Over-collection on a hit.** Finding a `.sql` dump or `.env` does not license downloading it in full — that can cross into exfiltrating real user data. Prove the file is what it is with the minimum bytes needed (see Reporting), then stop.

**Re-running the same scan blindly wastes traffic and misses the real win.** Content discovery's value on a return visit is the *diff*, not the re-run. Keep structured output (`-O json`/`csv`) from each visit and compare runs: a newly appeared path is freshly shipped, least-reviewed code — the highest-value target on a mature program. Re-blasting the identical 220k list every week just burns rate budget; scan wide once, then diff narrow.

**Encoding/scheme mistakes that produce phantom results.** On a `--raw` request, forgetting `--scheme https` can send cleartext to a TLS port and report nothing-found as if the paths were absent; `--ip`/`--interface` pin the connection when DNS or multihoming would otherwise send you to the wrong host. Confirm you are hitting the intended origin before trusting an empty result as evidence of absence.

---

## Reporting evidence

Capture, per finding:

1. **The exact dirsearch invocation** — full command line including `-t`, `--max-rate`, wordlist, and extensions, so the triager can see you scanned responsibly and can reproduce.
2. **The tool's output line** — dirsearch's `status - size - path` row for the hit, ideally from a saved report (`-o run.txt`, or `-O json/csv` for structured evidence). Use `--full-url` so the path is unambiguous.
3. **A clean hand-verification** — a plain `curl -i` (or the `--replay-proxy` Burp entry) showing the full request and response headers + the first bytes of the body, proving the path responds as claimed and is not a calibration artifact. `--redirects-history` documents any redirect chain.
4. **Minimal proof of impact, not bulk data** — for a backup/config/VCS hit, the first lines that identify the file (a `.git/config` `[core]` block, an `.env` variable *name* with the value redacted, a `.sql` `CREATE TABLE`), never the whole file. For an admin panel, the rendered login/dashboard with your low-privilege context visible. For a 403 bypass, the paired `403` request and the `200` bypass request side by side.
5. **Scope and severity framing** — state which in-scope host, which program-authorized credentials/session, and map the class to the program's rubric (VCS/secret exposure High–Critical; reachable admin panel or 403 bypass Medium–High; directory listing Low–Medium). Note the dupe risk honestly.

Redact secrets in the report body, keep raw evidence in a local file referenced by hash, and clean up anything you created (test accounts, uploaded probes). A tight report with the command, the saved report line, and one verified request/response pair beats a screenshot of a 10,000-line scan every time.

---

## References

- dirsearch repository & README (Python 3.11+ requirement, default-list note) — https://github.com/maurosoria/dirsearch
- dirsearch options reference (all flags verified here) — https://github.com/maurosoria/dirsearch/blob/master/docs/options.md
- dirsearch wordlists doc (`%EXT%`, `-f`/`--force-extensions`, `--overwrite-extensions`, categories, default `db/dicc.txt`) — https://github.com/maurosoria/dirsearch/blob/master/docs/wordlists.md
- dirsearch usage doc (default threads 25 + DoS warning, recursion, calibration, `--skip-on-status 429`) — https://github.com/maurosoria/dirsearch/blob/master/docs/usage.md
- SecLists Discovery/Web-Content (external wordlists: common, raft, directory-list) — https://github.com/danielmiessler/SecLists
- Companion tool note for `.git/` confirmation — [[nuclei]]; host enumeration before scoped scanning — [[subfinder]], [[amass]]

*Research supplement added 2026-11-15, grounded in the dirsearch README, `docs/options.md`, `docs/wordlists.md`, and `docs/usage.md` at github.com/maurosoria/dirsearch (every flag verified against the upstream options reference); recursion traffic math and 403-bypass/reporting guidance are the author's (model: claude-opus-4-8).*
