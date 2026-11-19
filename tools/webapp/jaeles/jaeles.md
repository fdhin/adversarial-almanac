---
name: "jaeles"
categories:
  - blackarch-webapp
version: "234.d40305f"
homepage: "https://github.com/jaeles-project/jaeles"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-06-29"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "jaeles-project/jaeles"
github-stars: 2384
github-pushed: "2026-06-20"
github-archived: false
github-issues: 32
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# jaeles

> Signature-driven web-application scanner that mass-checks URLs against YAML rule packs.

## What it's for

Jaeles is an extensible web-application scanning and fuzzing framework written in Go. Instead of a fixed checks engine, it runs user-supplied signatures (YAML rule packs, chosen by regular-expression "selectors") against a single URL or a large list of URLs, which makes it well suited to sweeping recon output for known CVEs and misconfigurations. It produces text and HTML reports, accepts URLs from files or stdin, and has a `server` mode exposing a web UI. It does not crawl or discover targets itself — feed it URLs from your own recon pipeline. The upstream repository is no longer actively maintained; the author's README announcement points to a successor project.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Scan a single URL with signature selectors

Fire every signature whose selector matches one or more tags at a single target.

```
jaeles scan -s 'jira' -s 'ruby' -u https://target.example.com
```

Outcome: all signatures matching `jira` or `ruby` run against the URL; matches are printed as findings while the scan proceeds.

### Batch-scan a list of URLs

Check a whole recon list with bounded concurrency, saving results to a directory.

```
jaeles scan -c 50 -s 'fuzz/.*' -U urls.txt -o /tmp/jaeles-out
```

Outcome: each URL in `urls.txt` is tested with the selected signatures, 50 concurrent connections at a time; findings and report files land under `/tmp/jaeles-out`.

### Include and exclude signature groups

Combine several selectors while excluding a noisy family of checks.

```
jaeles scan -c 50 -s 'java' -x 'tomcat' -U urls.txt
```

Outcome: signatures matching `java` run against every listed URL except those also matching the `tomcat` exclusion selector.

### Pipe URLs and route traffic through a proxy

Feed a filtered URL stream straight from stdin into a proxied scan.

```
cat urls.txt | jaeles scan -c 100 -s 'fuzz/.*' --proxy http://127.0.0.1:8080
```

Outcome: URLs are read from stdin and every request goes through the local proxy (for example Burp), so signatures can be reviewed live in the proxy history.

### Serve the web UI for interactive triage

Start the scan server over a signature set instead of running one-shot CLI scans.

```
jaeles server -s 'sensitive/.*' -L 2
```

Outcome: `jaeles server` starts the long-running web interface, where scans over the selected signatures can be launched and results triaged in the browser. Run `jaeles scan --help` first to confirm the UI port and signature paths configured for your install.

---

## Bug bounty workflow with jaeles

Jaeles is a signature *runner*, not a crawler and not a decision engine: it fires YAML rule packs at URLs you already hand it and prints what matched. On a bug bounty program that makes it a second-pass confirmation and niche-check tool, not a first reach — the signatures ship conditional JavaScript detection logic (baseline diffing, OOB callbacks, response-time deltas) that nuclei's matcher DSL expresses less directly, but the maintained corpus is nuclei's. Treat jaeles as the tool you reach for when you want *baseline-diff* detection, a specific jaeles signature pack, or to author one check with richer logic than a word/regex matcher. The phases below assume written authorization, a program scope, and low-volume discipline throughout.

> **Maintenance status (read first).** The upstream `jaeles-project/jaeles` repository is **archived and no longer maintained**; its README points to a successor project (`vigolium`). The packaged `version: "234.d40305f"` in this article's frontmatter is a late-2026 BlackArch snapshot of that archived tree. Nothing here is receiving security fixes, and the community signature repo (`jaeles-project/jaeles-signatures`) is likewise frozen — its newest CVE signatures are 2020-era. Plan your coverage accordingly: jaeles is good for the checks it already carries and for custom signatures you write, not for fresh-CVE coverage.

### Phase 0 — Install, seed signatures, and audit them before running

Jaeles keeps config and signatures under `~/.jaeles/` (config file `~/.jaeles/config.yaml`, base signatures under `~/.jaeles/base-signatures/`). Seed them once:

```
jaeles config init
```

Outcome: pulls the default signature set and the web-UI build into `~/.jaeles/`. To point jaeles at a vetted local checkout instead (recommended on an engagement, so you control exactly what runs), clone and register it:

```
git clone --depth=1 https://github.com/jaeles-project/jaeles-signatures /opt/jaeles-sigs
jaeles config reload --signDir /opt/jaeles-sigs
```

**The non-negotiable security step:** jaeles detection expressions are JavaScript and signatures can invoke `StringGrepCmd`/`RegexGrepCmd` and `--found`/`-f` command execution — the upstream signatures page warns in as many words that a signature can *execute commands on your own machine*. Before you run a third-party pack against anything, read it. Grep the signature tree for command execution and exfil-shaped payloads so a poisoned rule can't turn your own box into the victim:

```
grep -rniE 'GrepCmd|exec|/bin/|curl |wget |nc ' /opt/jaeles-sigs
```

Outcome: you review every rule that shells out before it runs with your network identity. This is the single most important hygiene step with jaeles and the one most hunters skip.

### Phase 1 — Build the URL list from your own recon

Jaeles discovers nothing on its own. Feed it a deduplicated, in-scope, live-host list from your existing pipeline (subfinder → httpx for liveness, or your crawler's output). Keep the list strictly inside the program's scope — jaeles will cheerfully hit every line you give it.

```
httpx -silent -l hosts.txt -mc 200,301,302,401,403 > live.txt
```

Outcome: `live.txt` holds only responsive, in-scope URLs. That file is the `-U` input for everything below. Scope filtering happens *here*, before jaeles ever runs; jaeles has no scope allowlist of its own.

### Phase 2 — Low-noise passive and sensitive-file first pass

Start with the quietest, highest-signal signatures — exposure checks that are a single GET each — at a throttled rate. The `sensitive/` and selected `common/` exposure signatures are the sweet spot: one request, high-value when they hit, low dupe-risk framing when you contextualize them.

```
jaeles scan -s 'sensitive/.*' -U live.txt -c 10 --delay 1 --timeout 15 -o out-sensitive
```

Outcome: every `sensitive/*` signature (`env-exposed`, `sql-backup-file`, `gitleak`, `svnleak`, `swagger-ui-probing`, the `secret-path-*` probes, etc.) runs against each URL, capped at 10 concurrent requests with a 1-second delay. Findings print live and land under `out-sensitive/`. Keep `-c` low and `--delay` non-zero on shared program infrastructure.

### Phase 3 — Stack-targeted selection

Fingerprint first (whatweb/httpx tech detection), then select only the signature families that match the stack. Selectors are regex-ish path matches against the signature tree, repeatable with multiple `-s`, narrowed with `-x`:

```
jaeles scan -s 'common/grafana.*' -s 'cves/grafana.*' -U grafana-hosts.txt -c 10 --delay 1 -o out-grafana
```

Outcome: only Grafana-relevant signatures run, against only the hosts you fingerprinted as Grafana. This is how you keep volume and dupe-risk down — never fire `cves/.*` blindly at an entire scope.

### Phase 4 — Single-target verification through a proxy

When a signature hits, re-run *that one signature* against *that one URL* with verbose output and the traffic routed through Burp/ZAP, so you capture the exact request/response pair for the report and confirm it is not a false positive:

```
jaeles scan -s 'sensitive/env-exposed.yaml' -u https://app.example.com -v --proxy http://127.0.0.1:8080 --save-raw -o verify
```

Outcome: the single check runs once, every byte is visible in the proxy history, and `--save-raw` writes the raw request/response to disk for evidence. This is your PoC-capture step — isolate to one signature and one URL so there is no ambiguity about what produced the finding.

### Phase 5 — Blind/OOB checks with a collaborator

Some signatures (SSRF, blind injection) resolve only out-of-band. Jaeles supports OOB via the `Collab("{{.oob}}")` detection and a `--params`/`-p` injected callback host. Use a canary you control, one request at a time:

```
jaeles scan -s 'fuzz/ssrf.*' -u https://app.example.com -p 'dest=YOUR-OOB.oast.example' -v -o out-oob
```

Outcome: the payload embeds your OOB host; a pingback to your collaborator confirms blind interaction. Fuzz-type signatures are the noisiest and most likely to generate load — scope them to a single URL during verification, never across a list.

### Phase 6 — Consolidate and report

Export machine-readable output, then write one report per confirmed finding (not per raw hit). `--json` plus `--html` give you both a parseable record and a browsable summary:

```
jaeles scan -s 'sensitive/.*' -U live.txt -c 10 --delay 1 --json --html --report jaeles-report.html -o out-final
```

Outcome: JSON findings plus an HTML summary under `out-final/`. Triage from these; never paste a raw jaeles hit into a report without the Phase 4 verification behind it.

## Where the money is with jaeles

Jaeles' payout surface is defined by what its frozen signature corpus actually covers — overwhelmingly *exposure and misconfiguration* checks plus a 2020-era CVE set. Prioritize by that reality:

| Priority | Finding class | Signature selector / feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Exposed secrets / config files | `-s 'sensitive/env-exposed.yaml'`, `-s 'sensitive/dot-config-exposed.yaml'`, `-s 'common/laravel-env.yaml'`, `-s 'common/nginx-conf-exposed.yaml'` | High–Critical | Medium (automated scanners find these) |
| 2 | Unauthenticated admin/API panels | `-s 'common/.*unauth.*'` (docker-api, kube-api, hadoop-unauth, netdata-unauth, elastic/kibana) | High–Critical | Medium–High |
| 3 | Backup / source leaks | `-s 'sensitive/sql-backup-file.yaml'`, `-s 'sensitive/gitleak.yaml'`, `-s 'sensitive/svnleak.yaml'` | High | Medium |
| 4 | Debug/trace interfaces | `-s 'common/django-debug-toolbar.yaml'`, `-s 'common/phpdebug.yaml'`, `-s 'common/symfony-debug.yaml'`, `-s 'common/go-pprof-exposed.yaml'` | Medium–High | Medium |
| 5 | Known-product CVEs (2020-era) | `-s 'cves/.*'` scoped to a fingerprinted product (grafana LFI, jira, jenkins, kong) | High | **High** — old, widely scanned |
| 6 | SSRF / blind injection (OOB) | `fuzz/*` signatures + `Collab()` + `-p 'dest=...'` | High | Low (needs real interaction) |
| 7 | Directory listing / info disclosure | `-s 'common/directory-listing.yaml'`, `-s 'common/phpinfo.yaml'`, `-s 'common/aws-s3-open-bucket.yaml'` | Low–Medium | High |
| 8 | Subdomain takeover | `-s 'sensitive/subdomain-takeover.yaml'` (DNS detection via `DnsRegex`) | High | High (many tools cover it) |

The honest read: priorities 1–3 and 6 are where jaeles earns its place; priorities 5, 7, and 8 are high-dupe territory where nuclei's larger, fresher corpus or a dedicated takeover tool usually got there first. **Note the gaps in the built-in corpus:** there is no dedicated `.git/config` directory-exposure signature (the `gitleak`/`svnleak` rules are secret-content probes, not VCS-directory probes), and GraphQL coverage is a single 2020 GraphQL Playground XSS CVE signature (`cves/graphql-playround-xss-cve-2020-4038.yaml`), not a GraphiQL/introspection check. If you want `.git` exposure or GraphQL introspection coverage, author it (next section) — that is exactly where jaeles' custom-signature strength pays off.

## Command recipes

Each block is annotated with the low-volume settings called out explicitly. Defaults matter here: `--concurrency/-c` defaults to **20**, `--threads` (concurrency *inside* one signature) to **10**, `--level/-L` to **1**, `--timeout` to **20s**. The 20×10 default fan-out is heavy for a bug bounty target — throttle deliberately.

**Throttled sensitive-file sweep (the safe default shape):**

```
jaeles scan -s 'sensitive/.*' -U live.txt -c 10 --delay 1 --timeout 15 -o out
```

`-c 10` halves the default concurrency; `--delay 1` inserts a 1-second gap between requests; `--timeout 15` fails slow hosts fast instead of piling up connections. Expected output: per-URL `[Found]` lines for any match, with results written under `out/`.

**Select by signature level (run only higher-confidence rules):**

```
jaeles scan -s 'common/.*' -U live.txt -L 3 -c 10 --delay 1 -o out
```

`-L 3` filters to signatures whose declared `level` is ≥ 3, skipping low-confidence noise (default is `-L 1`, which runs everything). Signature level is set in each rule's `info.risk`/level metadata; raising `-L` is the cleanest single lever for reducing false-positive volume.

**Include two families, exclude a noisy one:**

```
jaeles scan -s 'java' -s 'cves/jira.*' -x 'tomcat' -U java-hosts.txt -c 10 --delay 1
```

Multiple `-s` are OR'd; `-x 'tomcat'` drops any matched signature whose path also matches the exclusion. Use this to run a product's CVE pack while suppressing a sub-family that floods your console.

**Pipe from recon and proxy everything for live review:**

```
cat live.txt | jaeles scan -s 'sensitive/.*' -c 5 --delay 1 --proxy http://127.0.0.1:8080
```

URLs are read from stdin; every request traverses Burp so you can watch signatures fire in real time. `-c 5` keeps the proxy history readable and the target load trivial.

**Passive mode (response inspection without active probing):**

```
jaeles scan --passive -U live.txt -c 10 -o out-passive
```

`--passive` runs passive signatures (selected via `--sp`, default `*`) that inspect responses rather than sending crafted attack requests — the lowest-impact mode, appropriate as a first touch on sensitive scope.

**Single-signature verification with raw capture (PoC step):**

```
jaeles scan -s 'common/directory-listing.yaml' -u https://app.example.com -v --save-raw --no-db -o verify
```

One signature, one URL, verbose. `--save-raw` writes the request/response pair for evidence; `--no-db` skips the results database for a throwaway verification run. This is the command whose output goes into the report.

**Large input without a request flood (chunked run):**

```
jaeles scan -s 'sensitive/.*' -U huge-list.txt --chunk --chunk-size 20000 --chunk-threads 2 -c 10 --delay 1 -o out
```

`--chunk` processes a very large `-U` file in slices (`--chunk-size` lines each, default 20000) with a bounded number of chunk workers (`--chunk-threads 2`), so memory stays flat and you still control per-request concurrency with `-c`. Use it for big scopes without raising instantaneous load.

### Authoring a custom signature

The gap-fillers (a `.git/config` probe, a GraphQL introspection check) are a few lines of YAML. A signature's structure, grounded in the upstream format:

```yaml
id: custom-git-config-exposed
info:
  name: Exposed .git/config
  risk: High
  confidence: Tentative
  tech: general
requests:
  - method: GET
    redirect: false
    path: "{{.BaseURL}}/.git/config"
    detections:
      - >-
        StatusCode() == 200 &&
        StringSearch("response", "[core]") &&
        !StringSearch("response", "<html")
```

Key fields: `id` (what the `-s` selector matches on), `info` (`name`, `risk`, `confidence`, `tech`, `os`), and `requests[]` each carrying `method`, `path` (templated with `{{.BaseURL}}`/`{{.URL}}`), and `detections[]`. **Detections are JavaScript expressions that must return a boolean**, evaluated right after the response. Useful functions: `StatusCode()`, `StringSearch(where, needle)` / `RegexSearch(where, pattern)` (where ∈ `response`, `resHeaders`, `resBody`, `oResponse`…), `StringCount`/`RegexCount`, `ContentLength()`, `ResponseTime()`, the `Origin*` variants for baseline comparison, `ValueOf("name")`, and `Collab("{{.oob}}")` for OOB. Register it and run:

```
jaeles config add --signDir /opt/jaeles-sigs
jaeles scan -s 'custom-git-config-exposed' -u https://app.example.com -v
```

The baseline-diff style is jaeles' real differentiator — an `origin` request plus detections like `ContentLength() - OriginContentLength() > 5000` or `Math.abs(ResponseTime() - OriginResponseTime()) > 3` express "this response is meaningfully different from the control" in a way flat matchers cannot, and `--fi` (filtering mode) enables the `Diff()`-style comparison path. That is the logic worth writing a jaeles signature *for*, rather than reaching for nuclei.

## Pitfalls, noise and scope discipline

How hunters get reports rejected — or get themselves banned — with jaeles:

- **Running unaudited third-party signatures.** Jaeles detections are JavaScript and signatures can execute OS commands (`StringGrepCmd`, `--found`). A malicious or careless signature runs on *your* machine with *your* network identity. Audit the pack (Phase 0) before every engagement. This is a risk to you, not just the target.
- **The 20×10 default fan-out.** Default `-c 20` with `--threads 10` and no delay can hammer a small target hard enough to look like a DoS — a fast path to removal from a program. Always set `-c` low and `--delay ≥ 1` on bug bounty infrastructure, and reserve `fuzz/.*` for single-URL verification, never list-wide runs.
- **No built-in scope control.** Jaeles hits exactly the URLs in `-U`/stdin with no allowlist, regex scope, or out-of-scope guard. One stray line — a wildcard that resolved to a third party, an out-of-scope apex — is an unauthorized-access incident. Filter scope *before* jaeles, in your recon pipeline, and re-check `live.txt` by eye.
- **Firing the whole CVE pack blindly.** `-s 'cves/.*'` against an entire scope is maximum noise for maximum dupe-risk: the signatures are 2020-era, every scanner has them, and most hits are either patched or already reported. Fingerprint first, then scope `cves/*` to the matching product.
- **Reporting raw hits as findings.** Signature matches are *candidates*. The `secret-path-*`, `directory-listing`, and `*-probing` rules false-positive on soft-404s, catch-all pages, and WAF interstitials. A hit you did not reproduce through Phase 4 (single signature, proxy, `--save-raw`) is not a finding — submitting it unverified burns your reputation with the triage team.
- **Dupe-risk blindness on info-disclosure classes.** `phpinfo`, `directory-listing`, `aws-s3-open-bucket`, and `subdomain-takeover` are heavily automated across the industry; expect these to be known/duplicate unless your finding adds impact context. Lead with what the exposure *enables*, not the raw hit.
- **Stale-tool trap.** Because the project is archived, do not assume a jaeles negative means "not vulnerable" for anything post-2020 — its corpus simply doesn't cover it. Jaeles clears the checks it carries; it is not a substitute for a maintained scanner.

## Reporting evidence

For each confirmed finding, capture — from the Phase 4 single-signature verification, not the bulk run:

- **The exact command**, including the specific signature path (`-s 'sensitive/env-exposed.yaml'`) and the single target URL, so the triager can reproduce it deterministically.
- **The raw request/response pair** from `--save-raw` (or the Burp history export), trimmed to the proof — the matched string, the status code, the exposed bytes. Redact any secret you actually recovered; prove access without exfiltrating more than the match needs.
- **The signature's detection logic** quoted from the YAML, so the triager sees *why* jaeles flagged it (e.g. `StatusCode() == 200 && StringSearch("response", "DB_PASSWORD")`) — this pre-empts "false positive?" pushback.
- **The `--json` record** for the finding as a machine-readable artifact, and the `--html` report only as a supporting summary, never as the primary evidence.
- **Impact framing**, not the raw hit: what the exposed file/panel enables (credential reuse, internal pivot, data access), mapped to the program's severity rubric. For OOB findings, include the collaborator interaction log (DNS/HTTP pingback with timestamp) tying `{{.oob}}` to your canary host.
- **Scope attestation**: the in-scope status of the exact host, since jaeles has no scope guard of its own and the triager will check.

Never submit the bulk-scan console log as evidence — isolate, reproduce, and capture one clean request/response per report.

## jaeles vs. nuclei — when each fits

Both are signature/template-driven HTTP scanners; they are not interchangeable.

| Dimension | jaeles | nuclei |
|---|---|---|
| Detection model | JavaScript expressions returning booleans; baseline/origin diffing (`Diff()`, `--fi`), response-time deltas, `Math.abs` | YAML matchers (status/word/regex/`dsl`) + extractors; conditional `dsl` expressions |
| Corpus | `jaeles-signatures`, **frozen**, ~2020 CVEs, exposure-heavy | ProjectDiscovery templates, **actively maintained**, thousands of current CVEs |
| Maintenance | Repo **archived**; successor is `vigolium` | Actively developed, frequent releases |
| Rate/scope control | `-c`, `--threads`, `--delay`, `--timeout`, `--chunk`; no scope allowlist | `-rl`/`-rld` rate-limit, `-c`/`-bulk`, scope flags, project-config |
| Built-in UI | Yes — `jaeles server` web UI | No (findings to stdout/markdown/JSON; separate UIs exist) |
| Safety | Signatures can execute OS commands — audit required | Templates are sandboxed by default; `-code`/headless gated behind flags |

**Reach for nuclei by default** on bug bounty: it is maintained, its corpus is current, its rate-limit controls are finer (`-rl` requests/second), and its templates are safe to run unaudited. **Reach for jaeles when** you specifically want baseline-diff detection logic, you are running a particular jaeles signature pack, or you are authoring one check whose logic (multi-request state, response-size/time comparison against a control request, custom JS conditions) is awkward to express as a flat nuclei matcher. In practice: nuclei for breadth and freshness, jaeles for a handful of bespoke, logic-heavy checks you write yourself — after auditing everything you load.

## References

- jaeles GitHub repository (archived) — https://github.com/jaeles-project/jaeles
- jaeles documentation home — https://jaeles-project.github.io/
- jaeles signature structure — https://jaeles-project.github.io/signatures/
- jaeles detection syntax (JavaScript functions, selectors, `Collab`) — https://jaeles-project.github.io/signatures/detection/
- jaeles installation and config (`config init`/`add`/`reload`, `--signDir`) — https://jaeles-project.github.io/installation/
- jaeles-signatures repository (community signature packs; folders `common`, `cves`, `dns`, `fuzz`, `mics`, `passives`, `probe`, `routines`, `sensitive`) — https://github.com/jaeles-project/jaeles-signatures
- jaeles flag reference (concurrency/level/timeout/chunk/output defaults) — https://trickest.com/tools/jaeles
- nuclei (comparison reference) — https://github.com/projectdiscovery/nuclei

*Research supplement added 2026-11-16, grounded in the jaeles GitHub repo (README/archived notice), jaeles-project.github.io signature/detection/installation docs, the jaeles-signatures repository folder and file listings, and the Trickest jaeles flag reference (model: claude-opus-4-8).*
