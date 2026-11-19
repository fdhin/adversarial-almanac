---
name: "gowitness"
categories:
  - blackarch-webapp
version: "3.2.2.r0.g601c843"
homepage: "https://github.com/sensepost/gowitness"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: go
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "sensepost/gowitness"
github-stars: 4555
github-pushed: "2026-10-29"
github-archived: false
github-issues: 40
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 66
gh-activity-checked: "2026-11-09"
---

# gowitness

> Chrome-headless website screenshot utility with SQLite storage and a web-based report server, written in Go.

## What it's for

gowitness screenshots web interfaces at scale using headless Chrome: a single URL, a file or stdin list of targets, CIDR ranges, or services parsed from Nmap XML. Beyond PNGs it can capture request logs, headers, cookies and console logs, write results to SQLite, JSON Lines, CSV or stdout, and serve a web-based gallery with a REST API over everything stored in the database. It never tests the targets — it documents what they look like, which is what makes it the standard visual layer over large recon sweeps. This covers the 3.x command layout (`scan`/`report` subcommands); older 2.x releases used a flat `gowitness single <url>` form.

## Typical use cases

### Screenshot a single page into the database

```
gowitness scan single --url https://target.example.com --write-db
```

Outcome: renders the page headlessly, saves the screenshot under `./screenshots` and records the URL, response metadata and screenshot reference in the default SQLite database.

### Screenshot a whole target list

```
gowitness scan file -f urls.txt --write-db
```

Outcome: screenshots every URL in the file (`-f -` reads stdin), one PNG and one database row per target — the standard bulk pass over recon output.

### Browse the results in the report server

```
gowitness report server
```

Outcome: starts the local web report server over the SQLite database, giving a filterable screenshot gallery plus the JSON API at the printed listen address.

## Bug bounty workflow with gowitness

gowitness is not a discovery tool and not a scanner — it is the **visual triage layer** that turns a flat list of live web surfaces into a gallery you can eyeball in minutes. On a large program (hundreds to thousands of resolved hosts × ports) the bottleneck is attention, not requests: you cannot manually open 2,000 URLs, but you can skim 2,000 thumbnails and spot the login panels, admin consoles, default install pages, stack-trace error screens, and forgotten staging boxes that are worth a real look. Everything below assumes you already have **written authorization**, the host list is **inside the program's declared scope**, and you have confirmed the program permits automated screenshotting (most do; a few forbid headless-browser traffic — check the rules of engagement).

The golden property for staying safe: **gowitness makes exactly one navigation and one screenshot per URL.** One host = one Chrome page load. It does not crawl, does not fuzz, does not submit forms. That makes it inherently low-volume *per host* — the only way to turn it into an abusive tool is to point it at a huge expanded target set with high `--threads`, or at hosts outside scope. The whole discipline is in the target list and the concurrency number.

### Phase 0 — Scope intake and target-list hygiene (where bans happen)

Before gowitness ever runs, decide exactly what it is allowed to touch. The single most common way to get a report rejected (or get removed from a program) with a screenshot tool is capturing an **out-of-scope** asset — a shared-hosting neighbour, a third-party SaaS login, an acquired-company domain the program hasn't onboarded. gowitness will screenshot anything you feed it, including a parked domain or a CDN edge that belongs to someone else.

- Keep an explicit `in-scope.txt` and run everything through it. If the program gives CIDRs, remember that `scan cidr` expands a `/24` into 256 hosts × every port in `--port` — a `/16` is 65,536 hosts. Expansion is where a "polite" tool becomes a sweep. Use TEST-NET ranges like `198.51.100.0/24` only for your own dry runs.
- gowitness does **no liveness check of its own** before launching Chrome — it will spend a full `--timeout` (default 60s) on every dead host. Pre-filter with a liveness/HTTP prober (httpx) so Chrome only visits hosts that actually answer. This is both faster and quieter.
- Resolve the "is this in scope?" question per host *before* the run, not after you have a screenshot of someone else's login page sitting in your database.

### Phase 1 — Feed gowitness from a live-host prober (httpx)

gowitness consumes a list; httpx produces one. The canonical pairing filters to live HTTP(S) surfaces first, then pipes straight into gowitness over stdin (`-f -`):

```
httpx -l in-scope.txt -silent -threads 25 | gowitness scan file -f - --write-db --write-jsonl
```

Outcome: httpx discards dead hosts and emits live URLs with their scheme already attached; gowitness screenshots each one, writing a PNG/JPEG plus a row to SQLite and a line to `gowitness.jsonl`. Because httpx has already confirmed liveness, you pay almost no `--timeout` penalty on dead hosts. Keep httpx's own concurrency sane (`-rate-limit`/`-threads`) — it is the component actually hammering the target, not gowitness.

### Phase 2 — Chunked screenshot run (checkpoint, resume, stay low-volume)

For anything beyond a few hundred URLs, do not run one giant pass. Split the live list into batches so you can checkpoint, throttle, and abort cleanly if the program's traffic expectations are tight:

```
split -l 250 live.txt batch_
for b in batch_*; do
  gowitness scan file -f "$b" -t 4 -T 45 --delay 2 \
    --write-db --write-db-uri sqlite://gowitness.sqlite3 --write-jsonl
  sleep 30
done
```

`-t 4` holds concurrency to four headless tabs (default is `6`); `-T 45` caps each page at 45s; `--delay 2` waits two seconds after navigation before the shot so JS-rendered panels paint. All batches write into the **same** SQLite DB, so the report server sees one unified gallery. This is the low-volume posture: a handful of concurrent page loads, a pause between batches, one screenshot per host.

### Phase 3 — Triage: pull login panels and error pages out of the gallery

Two triage surfaces over the same database. The **report server** is the visual one — a filterable thumbnail gallery; the **`report list`** CLI table is the grep-able one, which matters when you want to script the first pass:

```
gowitness report server --host 127.0.0.1 --port 7171 --db-uri sqlite://gowitness.sqlite3
gowitness report list --db-uri sqlite://gowitness.sqlite3 | grep -iE 'login|sign ?in|admin|dashboard|jenkins|grafana|kibana|portal'
```

`report list` prints columns `When · Failed · Code · Input URL · Title · ~Size · Net · Con · Header · Cookie`, so you can grep titles for panel keywords and filter the `Code` column for the error classes that leak internals (`401/403` auth walls worth probing, `500/502/503` crash screens that often carry stack traces). Browse the thumbnails for anything that *looks* like a default install page, a dev/staging banner, a directory listing, or an unauthenticated dashboard — the visual signal catches what keyword filters miss.

### Phase 4 — Correlate with nuclei (visual confirmation both directions)

gowitness and nuclei are complementary and feed each other:

- **nuclei → gowitness:** nuclei flags an exposed-panel / takeover / default-login template match; screenshot every matched URL to confirm it is real and not a wildcard/redirect false positive before you write the report: `nuclei -l live.txt -t http/exposed-panels/ -silent | gowitness scan file -f - --write-db`.
- **gowitness → nuclei:** the gallery surfaces a panel you can't fingerprint by eye; feed that shortlist back into nuclei's technology/panel templates to identify the product and pull any version-specific checks.

The screenshot is the triager-facing artifact; the nuclei match is the machine-readable one. A report that pairs "nuclei matched `grafana-unauth`" with a thumbnail of an open Grafana dashboard is far harder to dismiss than either alone.

### Phase 5 — Validate the candidate, then package evidence

gowitness confirms *what a surface looks like*, never that it is vulnerable. A screenshot of a login page is not a finding; a screenshot of an **authenticated** admin dashboard reached with no credentials is. Validate manually with single, low-volume requests (the underlying bug class — default creds, missing auth, info disclosure — is tested by hand, per that class's methodology), then export the gowitness evidence for the report (Phase "Reporting evidence" below).

### Worked example — 1,200 hosts down to three leads

A compact end-to-end pass that shows how the phases chain, using a TEST-NET stand-in for an authorized scope:

```
# 0. Scope intake — resolve to an in-scope host list (your own process)
wc -l in-scope.txt                      # 1,200 candidate hosts

# 1. Liveness + scheme via httpx, piped straight in; chunked by Phase 2 loop
httpx -l in-scope.txt -silent -threads 25 -o live.txt
wc -l live.txt                          # 418 actually answer

# 2. Low-volume screenshot run into one DB
split -l 200 live.txt batch_
for b in batch_*; do
  gowitness scan file -f "$b" -t 4 -T 40 --delay 2 --write-db --write-jsonl
  sleep 20
done

# 3. First triage pass — titles and codes
gowitness report list --db-uri sqlite://gowitness.sqlite3 \
  | grep -iE 'login|admin|grafana|jenkins|kibana|index of'   # ~12 candidates
#   browse thumbnails in the server for the dozen, discard redirects/parked

# 4. Confirm the two that look unauthenticated with nuclei, visually re-check
printf '%s\n' https://dash.example.com https://ci.example.com \
  | nuclei -silent -t http/exposed-panels/ \
  | awk '{print $NF}' | gowitness scan file -f - --write-db

# 5. Validate by hand, then package
gowitness report generate --db-uri sqlite://gowitness.sqlite3 --zip-name leads.zip
```

The funnel — 1,200 → 418 live → ~12 interesting thumbnails → 2 validated leads — is the whole value proposition: gowitness collapses "look at everything" into "look at twelve pictures," and the two findings that survive manual validation carry a screenshot, a DB row, and a nuclei match into the report.

---

## Where the money is with gowitness

gowitness finds nothing by itself — it *surfaces candidates* for other bug classes by making them visible at scale. Severities below are the severity of the underlying finding the screenshot reveals, not of the screenshot. Dupe risk is uniformly non-trivial: screenshot triage (gowitness/EyeWitness/Aquatone) is the first thing every hunter runs on a new scope, so the obvious panels are found fast and often.

| What the thumbnail reveals | Feature/flag that reaches it | Typical severity of the real bug | Dupe risk |
|---|---|---|---|
| Unauthenticated admin dashboard / console (Grafana, Kibana, Jenkins, Consul, Airflow) | `scan file` + gallery triage; grep `Title` for product names | High–Critical | High — top of everyone's list |
| Default-credential login panels (routers, PACS, printers, appliances) | gallery visual scan; `report list` title grep | Medium–High | High |
| Dev/staging/UAT environments exposed to the internet | thumbnails showing "staging"/"dev" banners; cert/host naming | Medium–High (weaker auth, debug on) | Medium |
| Stack-trace / debug error pages (500s leaking paths, SQL, versions) | `--http-code-filter 500 502 503` or `report list` `Code` column | Low–Medium (info disclosure; chains) | Medium |
| Directory listings / exposed file indexes | visual "Index of /" thumbnails | Low–High (depends what's listed) | Medium |
| Forgotten/abandoned apps (old frameworks, EOL versions) | visual fingerprint → nuclei version checks | varies; often High via known CVE | Low–Medium |
| Subdomain-takeover candidate pages (hosting 404/claim screens) | screenshot of the takeover provider's "no such app" page | High | High (automated hunters) |
| Internal tooling leaked to the edge (status pages, phpMyAdmin, monitoring) | gallery + `--chrome-header` for vhost probing | High | Medium |
| Login panels worth credential-stuffing *review* (not stuffing) | title grep for `login`/`sign in` | class-dependent | High |

The reusable insight: gowitness's payout is **coverage and speed of judgement**, not depth. It lets you look at every door in scope and decide which three are worth walking up to — the actual bounty comes from the manual test behind the interesting thumbnail.

---

## Command recipes

Every block below is one real command and what it produces. Low-volume settings are called out inline; the only concurrency control gowitness has is `--threads`, so politeness lives in that number, in the size of the list you feed it, and in keeping the list in scope.

**Single host into the DB (the smoke test).**

```
gowitness scan single --url https://target.example.com --write-db --screenshot-path ./shots
```

One navigation, one screenshot saved under `./shots`, one SQLite row in `gowitness.sqlite3`. Use this to confirm Chrome works and the target renders before a bulk run. `--screenshot-format` defaults to `jpeg` at `--screenshot-jpeg-quality 60`; add `--screenshot-format png` if you need lossless panel text for the report.

**Bulk, low-and-slow, multi-format output.**

```
gowitness scan file -f live.txt -t 4 -T 45 --delay 2 \
  --write-db --write-jsonl --write-csv
```

Four concurrent tabs, 45s per-page ceiling, 2s render delay. Writes the SQLite DB (`--write-db-uri` default `sqlite://gowitness.sqlite3`), a JSON Lines file (`gowitness.jsonl`), and a limited-column CSV (`gowitness.csv`). JSONL is the format to keep — it round-trips losslessly and feeds `report convert`/`report generate`.

**Stream from a prober, no intermediate file.**

```
httpx -l in-scope.txt -silent | gowitness scan file -f - --write-db --write-stdout
```

`-f -` reads stdin; `--write-stdout` echoes successful results so you can tee them into a pipeline. Nothing touches disk except the DB and the screenshots.

**CIDR sweep — handle with care.**

```
gowitness scan cidr -c 198.51.100.0/24 -p 80 -p 443 -t 4 --random --write-db
```

Expands the `/24` to 256 hosts × 2 ports = 512 page loads. `--random` spreads the targets so you are not hammering sequential addresses in one subnet. **Only run CIDR mode against ranges explicitly in scope** — this is the command that most easily becomes a sweep. For bigger lists prefer `--ports-small`/`--ports-medium`/`--ports-large` consciously; each tier multiplies the host count by its port count.

**From an Nmap XML (reuse a scan you already ran).**

```
gowitness scan nmap -f scope-scan.xml --open-only --service-contains http -t 4 --write-db
```

`--open-only` restricts to ports Nmap marked open; `--service-contains http` keeps only HTTP-ish services. `--hostnames` adds hostname-based URL candidates (useful against virtual-hosted targets). This avoids re-probing — you screenshot only what your authorized port scan already found live.

**Only screenshot interesting response codes.**

```
gowitness scan file -f live.txt --http-code-filter 200 --http-code-filter 401 \
  --http-code-filter 403 --write-db
```

`--http-code-filter` is a filter: by default *all* codes are screenshotted; specifying codes narrows to those. Handy to cut thousands of boring `301`/`404` thumbnails and focus the gallery on live apps and auth walls.

**Route every request through Burp (logging + scope enforcement).**

```
gowitness scan file -f live.txt --chrome-proxy http://127.0.0.1:8080 -t 2 --write-db
```

`--chrome-proxy` accepts `http://` or `socks5://`. Proxying through Burp gives you a full request/response log of every navigation (evidence, and a second place to enforce scope), at the cost of throughput — drop `--threads` to 2.

**Probe a virtual host without touching DNS.**

```
gowitness scan single --url https://198.51.100.10 \
  --chrome-header "Host: admin.example.com" --write-db
```

`--chrome-header` (repeatable) injects extra request headers — here a `Host` override to render a specific vhost on a shared IP. Useful when the interesting app only answers to a hostname the thumbnail of the bare IP never shows.

**Avoid the storage-explosion flags unless you need them.**

`--save-content` (saves every network-request body to the writers — "has the potential to make your storage explode in size", per the flag's own help) and `--screenshot-fullpage` both balloon output. For triage you want thumbnails, not a full mirror. Leave `--save-content` off; reach for it only when a specific response body is the evidence.

**Export a shareable, self-contained report.**

```
gowitness report generate --db-uri sqlite://gowitness.sqlite3 \
  --screenshot-path ./screenshots --zip-name gowitness-report.zip
```

Produces a ZIP (`--zip-name`, default `gowitness-report.zip`) containing `index.html`, its CSS, and a `screenshots/` folder — a static, offline report you can attach to a submission without standing up the server. More on this under Reporting evidence.

**Merge databases from parallel runs.**

```
gowitness report merge --source-path ./dbs --output-file merged.sqlite3
```

Combines multiple SQLite result DBs (from chunked or multi-box runs) into one. `--source-file` (repeatable) picks individual files instead of a directory. Point the report server at `merged.sqlite3` for a single gallery.

**Convert between SQLite and JSONL.**

```
gowitness report convert --from-file gowitness.sqlite3 --to-file results.jsonl
```

Direction is inferred from the extensions (`.sqlite3` ⇄ `.jsonl`); both must be given and must differ. Use it to pull a DB into greppable JSONL, or to rebuild a DB from a teammate's JSONL.

---

## Pitfalls, noise and scope discipline

**Out-of-scope captures are the fastest way to a ban.** gowitness screenshots whatever is on the list — a parked domain, a shared-hosting neighbour, an acquired company not yet onboarded, a third-party SaaS login. Filter the target list through the program's declared scope *before* the run; a database full of other people's login pages is both a rules violation and a privacy problem you created. CIDR and Nmap input are the riskiest because they expand: a `/16` is 65,536 hosts, and `--port` / `--ports-large` multiply that again.

**High `--threads` on a big list is the DoS vector.** Per host gowitness is one page load, but `--threads 50` against 5,000 hosts is 50 concurrent headless Chrome tabs hammering the scope. There is no rate-limit flag — concurrency *is* the throttle. Keep `--threads` low (2–6), chunk large lists with `split`, and pause between batches. If the program publishes a request-rate cap, honour it at the prober (httpx) stage, since that is what generates liveness traffic.

**Dead hosts burn a full timeout each.** Without a liveness pre-filter, every unresponsive target costs up to `--timeout` seconds (default 60) of a Chrome tab sitting open. On a list with many dead hosts this both wastes hours and keeps more concurrent tabs alive than you intended. Always prefilter with httpx; lower `--timeout` (e.g. `-T 30`) when the scope is known-responsive.

**The report server has no authentication and binds where you tell it.** `report server` defaults to `--host 127.0.0.1` (local only) — good. If you set `--host 0.0.0.0` on a shared box, cloud VM, or jump host, you have published every screenshot, URL, header, and cookie you captured to anyone who can reach port `7171`, with no login. That can expose the *target's* sensitive data (and your scope) to the world — a self-inflicted disclosure. Keep it on localhost and use an SSH tunnel if you need remote access.

**`--save-content` and full-page shots explode storage.** The `--save-content` help literally warns it can "make your storage explode in size"; it saves every network response body. `--screenshot-fullpage` multiplies image sizes. For triage you want small thumbnails and small DBs — leave both off unless a specific body/long page is the evidence.

**A screenshot is not a finding — and over-capturing can cross a line.** An image of a login page proves a login page exists, nothing more. Do not submit "found admin panel" with only a thumbnail; validate the actual bug (default creds, missing auth, info disclosure) by hand first. Equally, do not let gowitness *interact* beyond the single page load it does — it will not submit forms, and you should not bolt on `--javascript` to auto-login or scrape unless the program explicitly allows it. The `--javascript`/`--javascript-file` hook runs arbitrary JS in-page before the shot; that is active manipulation of the target and belongs only in authorized, documented testing.

**Headless Chrome is loud and fingerprintable.** The default `--chrome-user-agent` advertises a specific Chrome build; defenders see a burst of identical headless hits. This is fine on a program that permits automated traffic — but do not pair it with any attempt to look like "a real user" to evade detection, which crosses from testing into evasion. Keep your traffic honest and attributable to your researcher identity where the program expects it.

**Default JPEG quality can blur panel text.** The default `--screenshot-format jpeg --screenshot-jpeg-quality 60` compresses; small text on a dashboard can smear. For evidence screenshots that need legible version strings or error text, re-capture the specific host with `--screenshot-format png`.

---

## Reporting evidence

gowitness's output *is* the evidence package for the recon phase — capture it deliberately:

- **The screenshot itself**, at legible quality. For the hero image in a report, re-shoot the one host with `--screenshot-format png` so compression doesn't obscure the panel title, version banner, or stack-trace text. Crop/redact anything beyond what the proof needs.
- **The database row / JSONL line** for the host: it records the final URL (after redirects), HTTP status, page title, response headers, and cookies. `gowitness report list --db-uri sqlite://gowitness.sqlite3` prints this as a table (`When · Failed · Code · Input URL · Title · ~Size · Net · Con · Header · Cookie`); the JSONL line is the machine-readable version to paste into an appendix.
- **The request/response pair**, when you proxied through Burp (`--chrome-proxy`): attach the raw headers that prove, e.g., the dashboard returned `200` with no `WWW-Authenticate` challenge.
- **A self-contained report ZIP** as the evidence appendix: `gowitness report generate --db-uri sqlite://gowitness.sqlite3 --zip-name gowitness-report.zip` bundles `index.html` + screenshots into one offline archive the triager can open without running the tool. This is cleaner than a live server link and survives after your run.
- **Scope provenance.** Note which in-scope list and which program asset the host came from, so the triager can confirm you stayed inside the rules of engagement. For chained findings (gowitness surfaced it, nuclei fingerprinted it, you validated it), present all three artifacts together.

What *not* to include: raw dumps of every thumbnail in scope, out-of-scope captures, or `--save-content` bodies containing third-party data. The report carries the handful of hosts that matter and the single validated bug behind each — not your entire capture database.

---

## Headless-Chrome footprint and scaling notes

The engine underneath is a headless browser, and a few flags decide how heavy, how fast, and how reusable each run is:

- **Driver.** `--driver` defaults to `chromedp` (CDP over a managed Chrome); `gorod` (go-rod) is the alternative. gowitness downloads a platform-appropriate Chrome automatically; point `--chrome-path` at a system binary to skip the download or pin a known version. On a locked-down test box without a bundled Chrome, `--chrome-path` is how you supply one.
- **Remote / shared Chrome.** `--chrome-wss-url` attaches to an already-running Chrome DevTools instance (one started with `--remote-debugging-port`). This lets several gowitness runs share one browser process, or run the browser in a container/sidecar while gowitness runs elsewhere — useful for scaling without each run spawning its own Chrome.
- **Session reuse — powerful and sensitive.** `--chrome-user-data-dir` points Chrome at an existing profile instead of a throwaway one, so a logged-in session is reused across the scan, and *the directory is left in place afterward*. That means you can screenshot authenticated views — but only where the program authorizes authenticated testing, and never with a profile holding credentials outside the engagement. Treat the leftover profile as sensitive artifact storage.
- **Viewport.** `--chrome-window-x` / `--chrome-window-y` (default `1280×720`) set the capture size; raise them for wide dashboards, pair with `--screenshot-fullpage` only when a long page's lower content is the evidence (it inflates image size).
- **Identity.** `--chrome-user-agent` sets the UA string (default advertises a specific desktop Chrome build) and `--chrome-header` adds arbitrary request headers (repeatable) — use the latter for `Host`/auth headers you are permitted to send, not to disguise the traffic's origin.

## Appendix — verified flag reference (v3 `cmd/` source)

The flags below are read directly from the v3 source files cited in References; defaults are the source defaults. `scan` persistent flags apply to every `scan` subcommand (`single`, `file`, `cidr`, `nmap`).

**`scan` — concurrency and timing (the low-volume knobs)**

| Flag | Short | Default | Effect |
|---|---|---|---|
| `--threads` | `-t` | `6` | Concurrent headless tabs — the only throttle; keep low on big lists |
| `--timeout` | `-T` | `60` | Seconds before a page is abandoned; dead hosts cost this each |
| `--delay` | — | `3` | Seconds after navigation before the screenshot (JS render time) |
| `--driver` | — | `chromedp` | Engine: `chromedp` or `gorod` |
| `--uri-filter` | — | `http,https` | Schemes allowed into the scan |
| `--log-scan-errors` | — | `false` | Log timeouts/DNS errors to stderr (verbose) |

**`scan` — screenshot output**

| Flag | Default | Effect |
|---|---|---|
| `--screenshot-path` / `-s` | `./screenshots` | Where PNG/JPEG files land |
| `--screenshot-format` | `jpeg` | `jpeg` or `png` (use `png` for legible evidence) |
| `--screenshot-jpeg-quality` | `60` | 1–100; low default can blur panel text |
| `--screenshot-fullpage` | `false` | Whole page vs viewport; inflates size |
| `--screenshot-skip-save` | `false` | Don't write files (pair with `--write-screenshots`) |

**`scan` — writers and storage**

| Flag | Default | Effect |
|---|---|---|
| `--write-db` | `false` | Write to SQLite (required for the report server) |
| `--write-db-uri` | `sqlite://gowitness.sqlite3` | SQLite/MySQL/PostgreSQL target |
| `--write-jsonl` / `--write-jsonl-file` | `false` / `gowitness.jsonl` | Lossless JSON Lines output |
| `--write-csv` / `--write-csv-file` | `false` / `gowitness.csv` | Limited-column CSV |
| `--write-stdout` | `false` | Echo successes for shell pipelines |
| `--write-screenshots` | `false` | Store shots via writers in addition to disk |
| `--http-code-filter` | `[]` (all) | Only screenshot these response codes |
| `--save-content` | `false` | Save response bodies — **storage explosion risk** |
| `--skip-html` / `--skip-network-logs` | `false` | Trim stored HTML / per-request logs |

**`scan` — Chrome / headless footprint**

| Flag | Default | Effect |
|---|---|---|
| `--chrome-path` | auto-download | System Chrome binary to use |
| `--chrome-proxy` | — | `http://`/`socks5://` proxy (route through Burp) |
| `--chrome-wss-url` | — | Attach to a remote running Chrome DevTools |
| `--chrome-user-data-dir` | throwaway | Reuse a profile/session; left in place after |
| `--chrome-user-agent` | desktop Chrome | UA string |
| `--chrome-header` | `[]` | Extra request headers, repeatable |
| `--chrome-window-x` / `-y` | `1280` / `720` | Viewport size |

**Per-subcommand inputs**

| Subcommand | Key flags |
|---|---|
| `scan single` | `--url` / `-u` |
| `scan file` | `--file` / `-f` (`-` = stdin), `--port` / `-p` (`[80,443]`), `--ports-small/-medium/-large`, `--no-http`, `--no-https` |
| `scan cidr` | `--cidr` / `-c` (repeatable), `--cidr-file` / `-f`, `--port` / `-p`, `--random`, port-list flags |
| `scan nmap` | `--file` / `-f`, `--open-only` / `-o`, `--service-contains`, `--service`, `--port`, `--exclude-port`, `--skip-port`, `--hostnames` |

**`report` subcommands**

| Subcommand | Purpose | Key flags |
|---|---|---|
| `report server` | Filterable web gallery + REST API | `--host` (`127.0.0.1`), `--port` (`7171`), `--db-uri`, `--screenshot-path` |
| `report list` | CLI results table (grep-able) | `--db-uri`, `--json-file` |
| `report generate` | Static HTML report ZIP (evidence appendix) | `--db-uri`, `--json-file`, `--screenshot-path`, `--zip-name` (`gowitness-report.zip`) |
| `report convert` | SQLite ⇄ JSONL | `--from-file`, `--to-file` |
| `report merge` | Combine SQLite DBs | `--source-file` (repeatable), `--source-path`, `--output-file` |

Root persistent flags on every command: `--debug-log` / `-D`, `--quiet` / `-q`, `--no-log-color`, `--profile`.

---

## References

- gowitness repository (sensepost/gowitness) — https://github.com/sensepost/gowitness
- gowitness README (quick start, writers, report viewer) — https://raw.githubusercontent.com/sensepost/gowitness/master/README.md
- `cmd/scan.go` (scan persistent flags: `--threads`, `--timeout`, `--delay`, `--driver`, screenshot/chrome/write options) — https://github.com/sensepost/gowitness/blob/master/cmd/scan.go
- `cmd/scan_single.go`, `cmd/scan_file.go`, `cmd/scan_cidr.go`, `cmd/scan_nmap.go` (per-subcommand flags) — https://github.com/sensepost/gowitness/tree/master/cmd
- `cmd/report_server.go` (`--host`, `--port 7171`, `--db-uri`, `--screenshot-path`) — https://github.com/sensepost/gowitness/blob/master/cmd/report_server.go
- `cmd/report_generate.go` (static HTML report ZIP, `--zip-name`) — https://github.com/sensepost/gowitness/blob/master/cmd/report_generate.go
- `cmd/report_list.go`, `cmd/report_convert.go`, `cmd/report_merge.go` (triage/convert/merge) — https://github.com/sensepost/gowitness/tree/master/cmd
- gowitness wiki (Usage, Report Server — note: 2024-era, predates the 3.x `scan`/`report` layout) — https://github.com/sensepost/gowitness/wiki
- httpx (live-host prober that feeds gowitness) — https://github.com/projectdiscovery/httpx
- nuclei (template matches to confirm visually) — https://github.com/projectdiscovery/nuclei

*Research supplement added 2026-11-16, grounded in the sensepost/gowitness v3 `cmd/` source (scan.go, scan_single.go, scan_file.go, scan_cidr.go, scan_nmap.go, report_server.go, report_generate.go, report_list.go, report_convert.go, report_merge.go, root.go), README, and wiki as cited above (model: claude-opus-4-8).*
