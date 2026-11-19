---
name: "dontgo403"
categories:
  - blackarch-webapp
version: "v2.0.1.r1.g3ee901f"
homepage: "https://github.com/devploit/nomore403"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "devploit/nomore403"
github-stars: 1919
github-pushed: "2026-10-30"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 20
gh-activity-checked: "2026-11-09"
---

# dontgo403

> Tool to bypass 40X response codes.

## What it's for

dontgo403 is a Go command-line tool for testing HTTP access-control bypasses: given a URL that returns `401`/`403` (or a related block), it replays the request through a large set of mutations — alternative HTTP methods, path suffixes and encodings, trust and override headers, absolute-form request targets, raw HTTP desync shapes — and highlights the responses most likely to represent a real bypass. It is built for bug bounty, penetration testing and regression testing of access-control rules: it captures a baseline, auto-calibrates against non-existent paths to suppress default-error false positives, scores the differences, and prints replayable `curl` commands for high-value findings. It does not "break authentication" by itself; it surfaces differences in how frontends, proxies, WAFs and backends interpret the same request. The upstream project was renamed from `dontgo403` to NoMore403 (the old `devploit/dontgo403` repository URL redirects there); the BlackArch package still builds and installs the current source under the `dontgo403` name, so the installed command remains `dontgo403`.

## Typical use cases

### Triage a blocked path for bypasses

The default run against a single 403-gated URL.

```
dontgo403 -u https://target.example.com/admin
```

Outcome: sends a baseline request, auto-calibrates the target's default error behavior, runs every technique, and ends with a `LIKELY BYPASS` summary (each entry with a score, a `403=>200`-style transition, the mutation that worked and a ready-to-run `curl` replay), an `INTERESTING VARIATIONS` section for parser/routing differences, and a count of techniques that produced nothing visible.

### Watch the traffic through an intercepting proxy

Run it verbose with all requests visible in Burp or another proxy.

```
dontgo403 -u https://target.example.com/admin -x http://127.0.0.1:8080 -v
```

Outcome: every probe appears in the proxy for manual inspection, and `-v` shows all surviving results instead of the filtered set. Raw-wire techniques (duplicate headers, absolute targets, desync forms) do not go through the upstream proxy.

### Run only chosen techniques

Limit the run to specific mutation families.

```
dontgo403 -u https://target.example.com/admin -k headers,absolute-uri,raw-desync
```

Outcome: executes only the named techniques (`-k` takes a comma-separated list — e.g. `verbs`, `endpaths`, `double-encoding`, `ip-encoding`, `http-versions`, `raw-duplicates`), which makes iterations against a known target much faster than the default full sweep.

### Replay a captured request file

Reproduce a blocked request exactly as captured, instead of a URL.

```
dontgo403 --request-file request.txt
```

Outcome: reads a Burp-style raw HTTP request, applies the mutation set to that exact request (headers and body included), and reports bypasses relative to its baseline — useful when the block depends on cookies or session state.

### Save findings as JSON Lines

Collect machine-readable results for pipelines.

```
dontgo403 -u https://target.example.com/admin --jsonl -o findings.jsonl
```

Outcome: writes one JSON object per result to `findings.jsonl` (`--json` produces a single structured document instead), suitable for import into triage tooling or long-term evidence storage.

No corpus evidence — derived from upstream documentation (current NoMore403 README; the BlackArch package builds the renamed source into the `dontgo403` command).
