---
name: "feroxbuster"
categories:
  - blackarch-webapp
version: "v2.13.1.r25.gb07a766"
homepage: "https://github.com/epi052/feroxbuster"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "epi052/feroxbuster"
github-stars: 8153
github-pushed: "2026-11-02"
github-archived: false
github-issues: 34
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# feroxbuster

> A fast, thread-parallel content discovery tool written in Rust that brute-forces directories and file names against a web server.

## What it's for

Finds hidden directories, files and endpoints on a web server by requesting wordlist entries, following recursion into discovered directories, and filtering responses by status code and size. Reach for it when enumerating a web root, an API surface, or extension-specific files (`.php`, `.bak`, `.txt`); it shows a live-updating results table while scanning. It is a discovery tool, not a vulnerability scanner — it reports what the server returns, and interpreting the findings is up to the operator. Link extraction from response bodies (`-e`/`--extract-links`) is enabled by default in current releases and can be turned off with `--dont-extract-links`.

## Typical use cases

### Recursive directory enumeration with a wordlist

Point it at a web root with a wordlist and let it recurse.

```
feroxbuster -u http://target.example.com -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words.txt
```

Outcome: scans the target recursively (default maximum recursion depth 4) and prints a live table of discovered paths with status code, size, word count and line count; each discovered directory is queued for scanning in turn.

### Hunt for files with specific extensions

Add extensions when the target's stack makes certain file types likely.

```
feroxbuster -u http://target.example.com -x php,html,txt -w /usr/share/seclists/Discovery/Web-Content/common.txt
```

Outcome: every wordlist entry is additionally requested with each extension appended (space- or comma-separated lists and repeated `-x` flags all work), surfacing `login.php` or `notes.txt` style files the bare wordlist would miss.

### Hide noisy status codes

Exclude the codes that flood output on wildcard-heavy or hardened targets.

```
feroxbuster -u http://target.example.com -w ~/wordlists/raft-medium-words.txt -C 403,404
```

Outcome: responses with status 403 or 404 are dropped from the results (`-C`/`--filter-status`); the include-only counterpart is `-s`/`--status-codes`, which shows only the listed codes.

### Scan a HTTPS host with a bad certificate

Skip TLS validation against internal hosts with self-signed or expired certificates.

```
feroxbuster -k -u https://target.example.com -w ~/wordlists/raft-medium-words.txt
```

Outcome: certificate validation is disabled (`-k`/`--insecure`) and the scan proceeds over HTTPS, with results identical to a validated target.

### Tune concurrency

Adjust thread count for speed or for fragile targets.

```
feroxbuster -u http://target.example.com -w ~/wordlists/raft-medium-words.txt -t 100
```

Outcome: raises concurrency to 100 threads (default 50); lower the value when the target rate-limits or errors out. `--auto-tune` lowers the rate automatically on excessive errors.

### Flat scan without recursion

Inventory only the top level of a large tree.

```
feroxbuster -u http://target.example.com -w ~/wordlists/raft-medium-words.txt -n
```

Outcome: `-n`/`--no-recursion` stops after the first directory level — a quick pass that lists what exists at the root without descending.

### Cap recursion depth

Bound how deep discovery descends.

```
feroxbuster -u http://target.example.com -w ~/wordlists/raft-medium-words.txt -d 2
```

Outcome: recursion is limited to 2 levels (`-d`/`--depth`; default 4, `0` means unlimited), keeping scans of deep directory trees bounded.

### Save results to a file

Keep a machine-readable record of the scan.

```
feroxbuster -u http://target.example.com -w ~/wordlists/raft-medium-words.txt -o found.txt --json
```

Outcome: every discovery is appended to `found.txt` as a JSON entry (plain text without `--json`), in addition to the terminal table — suitable for diffing between runs or feeding reports.

### Feed targets on stdin and pipe results onward

Chain it into a URL pipeline.

```
cat targets.txt | feroxbuster --stdin --silent -s 200 301 302 -x js
```

Outcome: reads one URL per line from stdin, reports only paths returning 200/301/302 (`-s`/`--status-codes`), and prints bare URLs only (`--silent`), so the output can be piped straight into another tool.

---

## Bug bounty workflow with feroxbuster

feroxbuster is a purely *active* tool — every result is a request that lands in the target's access logs. There is no passive mode. That single fact governs everything below: on an authorized program you treat each scan as a volume budget to be spent deliberately, not a fire-and-forget crawl. The default profile (50 threads per directory, unlimited concurrent directory scans, recursion depth 4, link extraction on) is tuned for speed on your own lab box, not for a shared production host under a bug-bounty rate rule. Reshape it before the first request.

The structural risk to understand first is **concurrency multiplication**. `-t`/`--threads` (default 50) is the number of active workers *per directory being scanned*. `-L`/`--scan-limit` (default `0` = unlimited) caps how many directories scan *simultaneously*. Recursion discovers new directories and queues each as its own scan. So the real in-flight request count is roughly **`threads × simultaneous-directory-scans`**, and with `--scan-limit 0` that second factor grows as recursion finds more directories. feroxbuster's own help frames it precisely: `--threads 30 --scan-limit 2` limits you to "a total of 60 active requests at any given time (threads × scan limit)." On a deep tree, default settings can put many hundreds of requests in flight at once — that is how hunters get rate-limited, blocked, or reported for degrading a service. Pin both numbers before you scan.

### Phase 0 — Scope lock and politeness budget (no requests yet)

Decide the limits before sending anything. Encode them so you cannot forget them mid-engagement:

- **Read the rate rule in the program's RoE.** Translate "max N requests/second" directly into `--rate-limit N` (requests per second *per directory*) and a conservative `-t`. If the program is silent, treat it as low-volume and cap hard anyway.
- **Bound the blast radius** with `-L`/`--scan-limit` (how many directories at once) and `--time-limit` (e.g. `--time-limit 30m`, which stops *all* scans at the deadline).
- **List destructive paths to exclude.** Content discovery sends `GET` by default (`-m`/`--methods`), and a `GET` to `/admin/users/1/delete`, `/logout`, `/api/jobs/purge`, or a one-click unsubscribe link can change state. Build a `--dont-scan` deny list (URLs or regex) for anything that looks state-changing, and never widen `--methods` to `POST`/`DELETE` on a live target without explicit permission.
- **Confirm the host list is in scope.** Link extraction (`-e`/`--extract-links`, on by default) stays on the target domain, but `--scope` *adds* domains to the in-scope set — only ever list assets the program names.

Persist the safe profile in a `ferox-config.toml` so every invocation inherits it (see Command recipes). A preset you cannot forget beats a flag you might.

### Phase 1 — Baseline and wildcard calibration

Learn what "not found" looks like before trusting any hit. Hit a path you know cannot exist and record the response:

```
curl -sI https://target.example.com/feroxbuster-does-not-exist-9z8y7x
```

Note the status, size, and whether the server returns a templated 200 "soft 404" instead of a real 404. feroxbuster auto-filters wildcard/soft-404 responses by default; **do not** reach for `-D`/`--dont-filter` (which *disables* that auto-filtering) unless you have a reason. When a target serves a soft-404 that slips past the wildcard filter, grab its URL for `--filter-similar-to` in Phase 3. This calibration step is what separates a report-quality finding from a page of 200s that are all the same catch-all template.

### Phase 2 — Flat sweep, no recursion

Inventory the top level first so you understand the shape of the target before you let it recurse. `-n`/`--no-recursion` stops after the first directory level:

```
feroxbuster -u https://target.example.com \
  -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt \
  -n -t 20 --rate-limit 20 -C 404 -o ferox-flat.json --json
```

A low `-t 20` with `--rate-limit 20` keeps you polite while you read the status-code distribution. From this pass you learn which codes dominate (feeding `-C`/`--filter-status` deny lists or `-s`/`--status-codes` allow lists), which sizes are noise (`-S`/`--filter-size`), and which top-level directories are worth descending into next.

### Phase 3 — Targeted recursive discovery

Now descend, but bounded on every axis. Enable recursion with a shallow depth, cap the number of simultaneous directory scans, and filter the soft-404 cluster you found in Phase 1:

```
feroxbuster -u https://target.example.com/app/ \
  -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words.txt \
  -d 2 -t 20 -L 2 --rate-limit 25 \
  --filter-similar-to https://target.example.com/app/soft404 \
  -C 404 -S 0 -o ferox-app.json --json
```

`-d 2`/`--depth 2` limits recursion to two levels (depth `0` is *infinite* — never use it on a live target). `-L 2` means at most two directories scan at once, so total in-flight stays near `20 × 2 = 40`. `--filter-similar-to` hashes the named page with Simhash and drops responses within a Hamming distance of 3 — the clean way to kill a soft-404 cluster that status/size filters cannot catch (it replaced the older ssdeep implementation in v2.8.0). Point feroxbuster at the *specific subtree* that looked interesting in Phase 2 rather than re-scanning the whole root recursively.

### Phase 4 — Dynamic expansion on promising trees

Once a subtree looks content-rich, let feroxbuster learn from the responses — still rate-capped. Three collection features turn one pass into a smarter second pass:

- `-E`/`--collect-extensions` — any discovered URL with an extension adds that extension to the try-list for future requests.
- `-B`/`--collect-backups` — for every found file, requests likely backup variants (defaults: `~`, `.bak`, `.bak2`, `.old`, `.1`, `.swp`). This is the single highest-value feature for payouts: it turns `config.php` into a probe for `config.php.bak`.
- `-g`/`--collect-words` — parses each valid response and adds words an NLP model judges "important" to the wordlist, surfacing target-specific paths a generic wordlist lacks.

The `--smart` preset bundles `--auto-tune --collect-words --collect-backups`; `--thorough` adds `--collect-extensions` and `--scan-dir-listings`. Both *increase* request volume, so pair them with `--rate-limit` and `--auto-tune` (which lowers the rate automatically when errors spike) rather than running them wide open:

```
feroxbuster -u https://target.example.com/app/uploads/ \
  -w raft-small-words.txt --smart --rate-limit 15 -d 1 -L 1 \
  -o ferox-collect.json --json
```

### Phase 5 — Triage, validate, and pivot

A 200 is not a finding. For every notable hit: re-request it manually, diff it against the Phase 1 baseline, and decide what it *is*. Pull any backup/source file (`.bak`, `.old`, `.zip`, `.sql`, `.git/config`, `.env`) and read it for secrets or logic. feroxbuster's job ends at "this path exists and returns this"; the vulnerability (exposed credentials, an unauthenticated admin panel, source disclosure, a forgotten staging API) is your interpretation. Feed discovered endpoints onward — parameter fuzzing to ffuf, the auth/BOLA tests from a framework methodology, templated checks to nuclei. Content discovery is the *input* to the money phases, rarely the payout by itself.

### Phase 6 — Evidence and resumability

Scan with `-o file --json` from the start so you have a machine-readable record to diff between runs and attach to reports. feroxbuster writes a `ferox-*.state` file; if a scan is interrupted (`Ctrl-C`, `--time-limit`, a dropped connection), resume exactly where it stopped with `--resume-from ferox-<ts>.state` instead of re-hammering paths you already covered. Re-running discovery periodically and diffing JSON output surfaces newly shipped paths — often the freshest, least-reviewed code on the target.

---

## Where the money is with feroxbuster

feroxbuster reaches *content*, not logic flaws — so its findings cluster around exposed files, forgotten endpoints, and misconfigured directories. The table is ordered by payout potential; note that content-discovery findings carry **high dupe risk** by default, because every hunter runs a discovery tool against the same wordlists. Depth (recursion, link extraction, collected words, backup probing) is where you out-run the crowd and reach paths the shallow scanners missed.

| Finding class | Feature / flag that reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| Exposed backup / source files (`.bak`, `.old`, `.zip`, `.sql`, `config.php~`) | `-B`/`--collect-backups`, `-x`/`--extensions` | High–Critical (source + secrets) | Medium |
| Exposed VCS / secrets (`.git/`, `.svn/`, `.env`, `.DS_Store`) | wordlist (`raft-*`, `.git`-focused lists) | High | High (everyone scans these) |
| Unauthenticated admin / debug / internal panels | recursion (`-d`) + `admin`/`internal` wordlists | Medium–High | Medium |
| Forgotten staging / versioned / legacy paths (`/v1/`, `/old/`, `/beta/`) | `-g`/`--collect-words`, recursion | Medium | Low–Medium (depth-dependent) |
| Undocumented API endpoints feeding BOLA/BFLA | recursion + `-e`/`--extract-links` + API wordlists | Varies (input to high-sev) | Low |
| Exposed logs / DB dumps / archives | `-x sql,log,gz,tar,zip`, `--collect-backups` | High | Medium |
| Directory listing enabled | `--scan-dir-listings` / detect-directory-listing | Low–Medium | High |
| Config / deploy artifacts (`.yml`, `.conf`, `Dockerfile`, `.htaccess`) | `-x` + `--collect-extensions` | Medium–High | Medium |
| Open redirects / interesting params in JS | `-e`/`--extract-links` (harvests URLs from bodies) | Low–Medium (input to redirect/SSRF) | Low |

The pattern: the **backup/source** and **collected-words** rows are where feroxbuster earns its keep, because they reach files and paths a one-pass generic wordlist does not. The `.git`/`.env` rows pay but are picked clean on mature programs — report them, but expect "duplicate."

---

## Command recipes

Every recipe below is annotated with the expected output and the low-volume setting that keeps it program-safe. Flag forms are verified against feroxbuster v2.13.1.

### A reusable safe-profile config file

Drop this at `~/.config/feroxbuster/ferox-config.toml` (the per-user location) so every scan inherits polite defaults. feroxbuster searches, in order: `/etc/feroxbuster/`, then `CONFIG_DIR/feroxbuster/` (`$XDG_CONFIG_HOME` or `~/.config` on Linux), then the directory next to the binary, then the current working directory — **later files win**, and any command-line flag overrides all of them. Keys use the long flag names with underscores:

```toml
# ferox-config.toml — bug-bounty-safe defaults
threads = 20
rate_limit = 25           # requests/sec per directory
scan_limit = 2            # max directories scanned at once
depth = 2                 # recursion depth; never 0 on a live target
timeout = 10
auto_tune = true          # back off automatically when errors spike
filter_status = [404]
extract_links = true
output = "ferox.json"
json = true

[headers]
User-Agent = "bugbounty-researcher (hackerone.com/your-handle)"
```

Expected effect: running a bare `feroxbuster -u https://target.example.com -w list.txt` now starts at 20 threads, 25 req/s, depth 2, two concurrent directory scans, with auto-tune on and JSON output — no per-command flags to remember. A per-target `ferox-config.toml` in the working directory can override one value (e.g. a stricter `rate_limit`) without touching the global file. **Set an identifying User-Agent**: many programs require a researcher handle in the UA so their SOC can distinguish authorized testing from an attack.

### Hard concurrency cap for a strict rate rule

```
feroxbuster -u https://target.example.com -w raft-medium-words.txt -t 10 -L 1 --rate-limit 10
```

Expected output: a live results table, but with at most **one** directory scanning at a time (`-L 1`) and 10 req/s. Per feroxbuster's docs, "to truly have only 30 active requests to a site at any given time, `-t 30 -L 1` is necessary" — `-L 1` collapses the concurrency-multiplication problem entirely. Use this profile when the RoE says "one request at a time" or the target is fragile.

### Auto-tune / auto-bail for an unknown-fragility target

```
feroxbuster -u https://target.example.com -w raft-medium-words.txt --auto-tune --rate-limit 30
```

Expected behavior: `--auto-tune` enforces a per-directory rate limit when error thresholds are crossed — the triggers (per directory, after at least 50 requests or the thread count, whichever is higher) are errors above half the thread count (min 25), ≥90% of responses being `403`, or ≥30% being `429`. The rate self-adjusts every `timeout/2` seconds and lifts after three consecutive clean upward steps. Since v2.13.1 `--rate-limit` acts as a hard ceiling on auto-tune. If you would rather stop than slow down, swap in `--auto-bail`, which *aborts* the offending directory scan instead of throttling it — the safer choice when a program treats sustained `429`s as abuse.

### Extension and backup hunting (source disclosure)

```
feroxbuster -u https://target.example.com -w raft-small-words.txt \
  -x php,bak,old,zip,sql,conf -B --collect-extensions --rate-limit 20 -d 1
```

Expected output: each word is tried bare and with every `-x` extension; `-B`/`--collect-backups` additionally probes `~`, `.bak`, `.bak2`, `.old`, `.1`, `.swp` for every file found; `-E`/`--collect-extensions` adds any newly seen extension to the try-list. A hit on `database.sql` or `config.php.bak` here is often your highest-value finding — read it before reporting to confirm it actually contains secrets or source.

### Killing a soft-404 cluster with similarity filtering

```
feroxbuster -u https://target.example.com -w raft-medium-words.txt \
  --filter-similar-to https://target.example.com/this-page-404s -C 404
```

Expected output: pages whose Simhash is within Hamming distance 3 of the named soft-404 page are dropped, collapsing a wall of templated 200s into the handful of genuinely distinct responses. Combine with `-S`/`--filter-size` (exact-size noise), `-W`/`--filter-words`, `-N`/`--filter-lines`, and `-X`/`--filter-regex` (`-X 'Access Denied'`) when a single similarity anchor is not enough. Note `--filter-similar-to` costs extra work per response; a few anchors are fine, many hurt throughput.

### Force recursion and detect directory listings

By default feroxbuster recurses into paths that look like directories. Two flags change that behavior when a target hides its structure. `--scan-dir-listings` forces scans to recurse *into* directory-listing pages (an exposed listing is itself a low-to-medium finding, and often a map of files you would never have guessed). `--force-recursion` attempts recursion on *all* found endpoints, not just the ones feroxbuster classifies as directories — useful when a framework serves directory-like paths without trailing slashes:

```
feroxbuster -u https://target.example.com -w raft-medium-words.txt \
  --scan-dir-listings --force-recursion -d 2 -L 1 --rate-limit 15
```

Expected output: discovered listing pages are descended into and odd non-slash endpoints are treated as recursion candidates. Keep `-d` shallow and `-L 1` tight — `--force-recursion` can balloon the directory queue, re-introducing the concurrency-multiplication risk if you leave `--scan-limit` unbounded.

### Diff scans over time to catch new endpoints

New routes are the freshest, least-reviewed code on a target. Scan with JSON output, keep the files, and diff:

```
feroxbuster -u https://target.example.com -w raft-medium-words.txt \
  -C 404 -o ferox-$(date +%F).json --json
# later run, then compare the discovered URLs:
# jq -r 'select(.type=="response") | .url' ferox-2026-11-16.json | sort > a.txt
# jq -r 'select(.type=="response") | .url' ferox-2026-11-23.json | sort > b.txt
# comm -13 a.txt b.txt   # paths present only in the newer scan
```

Expected output: the `comm -13` line lists URLs that appeared only in the later scan — newly shipped endpoints to prioritize before anyone else reviews them. Pace the re-scans to the program's rate rule; a weekly polite pass is plenty.

### Wordlist management and dynamic collection

`-w`/`--wordlist` may be supplied multiple times or as a comma-separated list, and *also* accepts a URL — feroxbuster merges every source and de-duplicates the result into one effective wordlist. That changes how you stage a scan: run **small before large**. A fast `raft-small-words.txt` pass maps the shape of the target and (with `-g`/`--collect-words`) seeds a target-specific vocabulary; only then spend the time on `raft-medium`/`raft-large`. Picking the right list matters more than list size — directory-oriented lists (`raft-*-directories.txt`) for mapping structure, word lists (`raft-*-words.txt`) for files, and purpose-built lists (`SecLists/Discovery/Web-Content/api/`, `.git`-focused lists) when you know the stack.

The three collection flags are a feedback loop that *grows the effective wordlist from the target's own responses* while the scan runs:

```
feroxbuster -u https://target.example.com -w raft-small-words.txt \
  -g -E -B --rate-limit 15 -d 2 -L 1
```

- `-g`/`--collect-words` feeds important response words back into the list, so a bespoke path like `/quarterly-reconciliation/` gets tried even though no stock wordlist contains it.
- `-E`/`--collect-extensions` learns the target's live extensions (`.aspx`, `.jsp`, `.json`) and appends them to every future request — you no longer have to guess `-x` up front.
- `-B`/`--collect-backups` turns each found file into a backup probe.

This is the core reason to reach for feroxbuster over a static one-pass fuzzer: the wordlist you finish with is richer than the one you started with. The cost is request volume — every collected word and extension multiplies the try-list — so keep collection scoped to a promising subtree and rate-capped, never wide open across a whole root (see Pitfalls).

### Authenticated discovery behind a login

Most of the interesting surface on a SaaS target sits behind auth, so discovery that sends only anonymous requests misses it. Carry the session into the scan with `-H`/`--headers`, `-b`/`--cookies`, or `-Q`/`--query`:

```
feroxbuster -u https://target.example.com/app/ \
  -w raft-medium-words.txt -b "session=<your-valid-cookie>" \
  -H "Authorization: Bearer <your-token>" --rate-limit 15 -d 2 -L 1
```

For a request shape that is hard to express in flags (multi-header, specific body, exotic method), capture one authenticated request from Burp and template every scan request on it with `--request-file raw.txt` (pair with `--protocol https`). Two cautions specific to authenticated scanning: your session can expire mid-scan — watch for a cliff where everything turns to `401`/`302`-to-login, then re-auth and `--resume-from` — and an authenticated `GET` to a destructive path is *more* dangerous than an anonymous one, because it will actually be authorized to act. Keep the `--dont-scan` deny list tight here.

### Route only findings into Burp for manual follow-up

Discovery produces a list; the testing happens in a proxy. Rather than replay every request through Burp (which clogs its history with thousands of 404s), send *only the interesting responses*:

```
feroxbuster -u https://target.example.com -w raft-medium-words.txt \
  --burp-replay -R 200,301,302,401,403 --rate-limit 20
```

Expected behavior: `--burp-replay` points `--replay-proxy` at `http://127.0.0.1:8080` (and sets `--insecure`), and `-R`/`--replay-codes` sends *only* the listed status codes through it — so Burp's history fills with just the hits worth testing by hand, while the scan itself runs full-speed over the proxy-free path. `-P`/`--replay-proxy` lets you aim at any proxy; `--burp` (vs `--burp-replay`) instead routes *all* traffic through Burp when you want the full record.

### Interpreting the results table

Each row is `STATUS  SIZE  words  lines  URL` (plus method). Read them, do not just collect them:

- **`301`/`302`** — a redirect; with `-r`/`--redirects` off (default) feroxbuster reports the redirect itself. A `301` from `/admin` → `/admin/` is normal; a `302` to `/login` marks an auth wall worth noting, and a redirect whose `Location` leaks an internal host or a token is a finding on its own.
- **`401` vs `403`** — `401` means auth is required (a channel to attack); a `403` on a path your role should reach is a function-level-authorization lead. A wall of `403`s that are all identical is a soft block — filter it with `--filter-similar-to` or `-X`.
- **`200` with a suspicious size/word/line count** — identical `SIZE`/`words`/`lines` across many paths is the signature of a catch-all template (soft 404); a `200` whose size is an outlier is the one to open first.
- **`--unique`** collapses responses to one per distinct body, a fast way to see how many *genuinely different* pages a noisy tree actually has before you triage.

### A worked concurrency budget

Make the `threads × scan-limit` math concrete before you scan. Say the program's RoE caps you at 25 requests/second and you want recursion:

- `--rate-limit 25` enforces the req/s ceiling *per directory*. With more than one directory scanning at once, the aggregate exceeds 25 — so also cap the directory count.
- `-L 2`/`--scan-limit 2` means at most two directories scan simultaneously; combined with the rate limit, aggregate throughput stays near 2 × 25 but the number of *connections* in flight is bounded by threads.
- `-t 15`/`--threads 15` caps in-flight requests per directory at 15, so worst-case concurrent connections ≈ `15 × 2 = 30`.
- `-d 2`/`--depth 2` stops recursion from discovering an unbounded number of directories to queue.

Net: a bounded, predictable load (~30 concurrent connections, ≤~50 req/s aggregate) instead of the default profile's potential for hundreds of concurrent requests. If you only remember one thing, remember `-L 1` collapses the whole problem to a single directory at a time.

### Operational gotchas

Three environment issues that look like target behavior but are yours to fix (all documented in the project FAQ):

- **"Too many open files" / file-descriptor exhaustion.** High thread counts consume OS file descriptors; on Linux/macOS raise the soft limit (`ulimit -n 10000`) before a large scan, or lower `-t`. A scan that dies early with descriptor errors is a local limit, not the target blocking you.
- **Connection-reset / "connection closed" floods.** Often the target (or a WAF) throttling you — the signal to *reduce* `-t` and add `--rate-limit`/`--auto-tune`, not to retry harder. On a bounty target, sustained resets mean you are being treated as abusive; back off.
- **Truncated large responses.** `--response-size-limit` defaults to 4 MB of body read; a huge file-listing or export page may be cut off. Raise it only when you specifically need the full body, since larger reads cost memory and time across a recursive scan.

### Pipeline: discovered live hosts → bounded discovery → onward

```
cat in-scope-hosts.txt | feroxbuster --stdin --silent -s 200 301 302 \
  --rate-limit 15 -d 1 -o discovery.json --json
```

Expected output: one polite, shallow pass per host from stdin, printing only live URLs (`--silent`) so the list pipes cleanly into the next tool, while the full JSON record lands in `discovery.json` for triage and diffing. Avoid `--parallel` (one child process per stdin URL) on a shared target — it multiplies your total request rate by the number of hosts and is how a "polite" per-host rate becomes an aggregate flood.

---

## Pitfalls, noise and scope discipline

How hunters get reports rejected, rate-limited, or banned with this tool — and the setting that prevents each:

- **Concurrency multiplication → accidental DoS.** The number one failure. Default `-t 50` with `--scan-limit 0` and recursion can put many hundreds of requests in flight on a deep tree, degrading the service. This reads as an attack, not research. **Fix:** always set `-L`/`--scan-limit` and `--rate-limit`; add `--time-limit`; prefer `--auto-bail` on fragile targets. Never run `-d 0` (infinite recursion) against a live host.
- **`GET` to a state-changing path.** Content discovery blindly requests wordlist entries; a `GET` to `/logout`, `/admin/cache/flush`, `/api/orders/1/cancel`, or an email unsubscribe link can change state or spam real users. **Fix:** build a `--dont-scan` deny list (URL or regex) for state-changing patterns; never broaden `--methods` beyond `GET` on a live target without written permission.
- **Running `-D`/`--dont-filter` by habit.** This *disables* wildcard auto-filtering and buries you in soft-404 200s — people confuse it with a "collect" flag. It is `--dont-filter`. **Fix:** leave auto-filtering on; use `--filter-similar-to` for soft-404s instead.
- **Wrong flag from memory.** `-E` is `--collect-extensions`, not link extraction (`-e`/`--extract-links`). `-D` is `--dont-filter`, not dynamic collection (`-g`/`--collect-words`). `-s` is the status *allow* list, `-S` is size filtering. Guessing a flag can silently change behavior — verify against `feroxbuster --help` or the config docs.
- **Scanning out of scope via `--scope`.** `--scope` adds domains to the in-scope set; link extraction otherwise stays on the target domain. Listing an asset the program does not own is a scope violation regardless of what you find there. **Fix:** only ever pass assets named in the program.
- **`--parallel` flooding a shared target.** One child process per stdin URL means per-host rate limits add up to an aggregate flood against shared infrastructure (same CDN/WAF/origin). **Fix:** avoid `--parallel` on bug-bounty targets; scan hosts sequentially with a per-host cap.
- **Over-collecting.** `--smart`/`--thorough`, `--collect-words`, and `--collect-backups` each multiply request volume. Great on a narrow subtree, abusive across a whole root. **Fix:** scope collection features to a specific interesting directory and keep `--rate-limit` + `--auto-tune` on.
- **Reporting a raw 200 as a vuln.** A discovered path is not a finding until you show *why* it matters. Unvalidated "interesting endpoints" dumps get closed as informational or N/A. **Fix:** triage every hit (Phase 5) and report the exposed secret/source/function, not the path.
- **No identifying User-Agent.** Unattributed high-volume scanning can trip abuse response and get your account actioned. **Fix:** set a UA with your researcher handle (many programs require it) via `-a`/`--user-agent` or the `[headers]` config table.
- **Running an unofficial binary.** The project's own notice warns that `feroxbuster.com` is **not affiliated** with it; official builds come only from the GitHub releases, `feroxbuster.pro`, or the listed package repositories (BlackArch, AUR, nixpkgs, Homebrew, apt). A tampered discovery tool sees every path, header, cookie, and token you feed it — treat its provenance as part of your own opsec. **Fix:** install only from an official source and verify it.

---

## Reporting evidence

Capture enough that a triager can reproduce the finding in one command and see exactly what the server returned — no more data than the proof needs.

- **The exact invocation.** The full `feroxbuster` command line (with the rate/thread/depth caps visible) so the triager sees it was low-volume and in-scope. This doubles as proof of good conduct.
- **The JSON record.** Run with `-o finding.json --json`; the entry for the discovered path carries URL, status, content length, word count, and line count. Attach the single relevant entry, not the whole scan.
- **A manual confirmation request/response pair.** A `curl -i <url>` showing the status line and key response headers, plus the relevant body excerpt (redacted). For a source/backup file, show the first lines that prove it is source or that it contains a secret — never dump the whole file or exfiltrate more than needed.
- **The baseline contrast.** The soft-404 / "not found" response from Phase 1 next to the finding, so "this path genuinely exists and differs" is self-evident and not a wildcard artifact.
- **Severity framing.** State what the path *is* and the concrete impact (source disclosure → which secrets; admin panel → what it lets an unauthenticated caller do), mapped to the program's rubric. "Exposed `/backup/db.sql.bak` containing production credentials" beats "interesting file found."
- **Cleanup note.** Confirm you did not alter state and that any file you retrieved was read only to the extent needed to prove impact.

---

## Choosing feroxbuster vs ffuf vs dirsearch

The three tools overlap but reward different jobs; on a real engagement you often run more than one. Cross-links: [[ffuf]], [[dirsearch]].

| Dimension | feroxbuster | ffuf | dirsearch |
|---|---|---|---|
| Language / model | Rust, thread-parallel, recursion-first | Go, fast single-position (or clustered) fuzzer | Python, directory-brute-forcer |
| Recursion | First-class, auto-queues discovered dirs (`-d`) | Opt-in (`-recursion -recursion-depth`) | Built-in (`-r`, `--recursion-depth`) |
| Link extraction from bodies | Yes, on by default (`-e`) | No | No |
| Dynamic wordlist growth | Yes (`--collect-words/-extensions/-backups`) | No | Extension-tag expansion only |
| Soft-404 handling | Simhash similarity filter (`--filter-similar-to`) | Size/word/line/regex filters, `-ac` auto-calibrate | Built-in heuristics + size filter |
| Arbitrary fuzz positions (params, headers, vhosts) | No — paths only | **Yes** — any `FUZZ` keyword, multiple positions | No — paths only |
| Politeness controls | `--rate-limit`, `--auto-tune`, `--auto-bail`, `--scan-limit`, `--time-limit` | `-rate`, `-p` delay, `-t` | `--max-rate`, `-t`, delay |
| Config presets | `ferox-config.toml` (layered search path) | `ffufrc` | CLI/`default.conf` |

Practical division of labor on a bounty target: reach for **feroxbuster** when the job is recursive content discovery with link-following and target-adaptive wordlists — the deepest path coverage with the least babysitting. Switch to **ffuf** the moment you need to fuzz something other than the path — query parameters (`?FUZZ=`), header values, POST bodies, or virtual hosts — which feroxbuster does not do. Keep **dirsearch** as a quick, dependency-light pass or when its built-in extension/wordlist conventions fit a specific stack. They are complementary: feroxbuster maps the tree and harvests endpoints, ffuf then fuzzes the parameters on the interesting ones it found.

---

## References

- feroxbuster repository (epi052/feroxbuster) — https://github.com/epi052/feroxbuster
- feroxbuster documentation (overview) — https://epi052.github.io/feroxbuster-docs/overview
- Configuration: ferox-config.toml (search order, keys) — https://epi052.github.io/feroxbuster-docs/configuration/ferox-config-toml/
- Configuration: threads and limits (`-t` × `-L` math) — https://epi052.github.io/feroxbuster-docs/configuration/threads-and-limits/
- Configuration: command-line reference — https://epi052.github.io/feroxbuster-docs/configuration/command-line/
- Examples: auto-tune / auto-bail (trigger criteria) — https://epi052.github.io/feroxbuster-docs/examples/auto-tune/
- Examples: rate-limit — https://epi052.github.io/feroxbuster-docs/examples/rate-limit/
- Examples: filter-similar (Simhash, Hamming distance ≤ 3) — https://epi052.github.io/feroxbuster-docs/examples/filter-similar/
- Examples: dynamic collections (collect-words/extensions/backups) — https://epi052.github.io/feroxbuster-docs/examples/dynamic-collections/
- Examples: extract-links — https://epi052.github.io/feroxbuster-docs/examples/extract-links/
- Examples: limit-scans (`--scan-limit`) — https://epi052.github.io/feroxbuster-docs/examples/limit-scans/
- Examples: resume-scan / `--resume-from` — https://epi052.github.io/feroxbuster-docs/examples/resume-scan/
- Examples: scan-dir-listings / detect-directory-listing — https://epi052.github.io/feroxbuster-docs/examples/scan-dir-listings/
- Examples: replay-proxy (`--burp`, `--burp-replay`, `-R`) — https://epi052.github.io/feroxbuster-docs/examples/replay-proxy/
- Examples: raw-request (`--request-file`) — https://epi052.github.io/feroxbuster-docs/examples/raw-request/
- Examples: unique-responses (`--unique`) — https://epi052.github.io/feroxbuster-docs/examples/unique-responses/
- Interpreting results (status/size/word/line columns) — https://epi052.github.io/feroxbuster-docs/interpreting-results/
- FAQ: file descriptors (`ulimit -n`) — https://epi052.github.io/feroxbuster-docs/faq/no-file-descriptors/
- FAQ: connection closed / resets — https://epi052.github.io/feroxbuster-docs/faq/connection-closed/
- feroxbuster clap parser source (flag definitions, verified) — https://raw.githubusercontent.com/epi052/feroxbuster/main/src/parser.rs

*Research supplement added 2026-11-16, grounded in the feroxbuster v2.13.1 README, GitHub Pages documentation (configuration + examples), and the clap parser source at github.com/epi052/feroxbuster (model: claude-opus-4-8). Compare/contrast tooling: [[ffuf]], [[dirsearch]].*
