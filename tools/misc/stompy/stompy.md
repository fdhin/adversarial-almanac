---
name: "stompy"
categories:
  - blackarch-misc
version: "0.0.4"
homepage: "https://lcamtuf.coredump.cx/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-12"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "ZephrFish/Stompy"
github-stars: 50
github-pushed: "2026-07-30"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stompy

> Black-box quality tester for WWW session identifiers: harvests tokens from a site or a file and grades their entropy with FIPS-140-2 and spectral analysis.

## What it's for

Stompy (Michał Zalewski) assesses session IDs and other tokens that are supposed to be unpredictable — web session cookies, password-reset tokens, prepaid voucher codes. Point it at a URL and it automatically locates session identifiers in URLs, cookies and form inputs, collects a statistically significant sample without user interaction, determines the alphabet (hex, base64, base32, decimal, mixed and even fractional-bit encodings), then runs NIST FIPS-140-2 PRNG tests, temporal bitstream analysis, spectral/phase tests for PRNG hyperplanes, and spatial correlation checks, finally rating how many bits of genuine entropy the tokens carry. It can equally analyze tokens collected by other means, replay an earlier capture, or replay a handcrafted HTTP request to reach tokens only issued after a POST login. No tool can prove a generator secure — stompy demonstrates flaws; a clean report is not a security proof.

## Typical use cases

### Assess a site's session identifiers in place

Harvest and grade tokens straight from a live application.

```
stompy http://target.example.com/app/
```

Outcome: tokens are collected automatically; a summary report prints on stdout (at most 10 detected problems per test) while the full report and the captured samples are saved to `stompy-<date>.log` and `stompy-<date>.dat` in the working directory.

### Analyze tokens gathered by other means

Grade identifiers you already collected (from logs, another sniffer, a non-HTTP protocol).

```
stompy -R tokens.txt
```

Outcome: the same statistical battery runs over the file's contents — one token per line, up to 100 characters; encode raw binary tokens (e.g. hex via `od`) before feeding them in.

### Reach tokens issued only after a POST

Test login flow identifiers that a plain GET never triggers.

```
stompy -p request.txt http://target.example.com/login
```

Outcome: the raw HTTP request in request.txt is sent instead of a vanilla GET (include `Connection: close` and a trailing blank line); the identifiers in the responses are harvested and analyzed as usual.

### Re-test a previous capture

Re-run the analysis on stored samples without generating new traffic.

```
stompy -A stompy-capture.dat
```

Outcome: the earlier `stompy-<date>.dat` capture is re-analyzed with the full test battery; no new capture file is written.

*No corpus evidence — derived from upstream documentation.*
