---
name: "ppfuzz"
categories:
  - blackarch-webapp
version: "31.80982ec"
homepage: "https://github.com/dwisiswant0/ppfuzz"
packaged-by: [blackarch]
blackarch-builddate: "2022-12-02"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "dwisiswant0/ppfuzz"
github-stars: 678
github-pushed: "2025-08-28"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ppfuzz

> Fast Rust scanner for client-side prototype pollution that also fingerprints script gadgets to gauge impact.

## What it's for

Bulk-checking URLs for client-side prototype pollution: for each target it injects object/pointer queries in a real (headless) browser, and when a sink accepts them it fingerprints the script gadgets in use and prints payload info showing how the pollution could escalate — XSS, filter bypass or cookie injection. Written in Rust around `chromiumoxide`, so Chrome/Chromium must be installed (the `CHROME` environment variable overrides the executable; otherwise common binary names are auto-discovered). It tests for the vulnerability's presence and known-gadget impact; it does not perform server-side prototype pollution testing.

## Typical use cases

### Scan a list of URLs

Feed candidate pages from a recon file.

```
ppfuzz -l urls.txt
```

Outcome: each URL is loaded headlessly, probed with pollution queries, and vulnerable ones are printed with the matched gadget and suggested payload impact; `-V` shows the version and `-h` the full option list.

### Pipe URLs from another tool

Chain it directly in a shell pipeline.

```
cat urls.txt | ppfuzz
```

Outcome: reads targets from stdin (equivalent to I/O redirection `ppfuzz < urls.txt`), so you can feed it live output from subdomain or parameter discovery; append `2>/dev/null` to suppress errors and see only vulnerable targets.

### Tune concurrency and timeout

Control load against large inventories or slow hosts.

```
ppfuzz -l urls.txt -c 10 -t 15
```

Outcome: scans with a concurrency of 10 (default 5) and a 15-second per-connection timeout (default 30) — raise `-c` for wide batches on owned infrastructure, lower `-t` to fail fast on dead hosts.

*Provenance: no corpus evidence — derived from upstream documentation (README options table, verified 2026-11).*
