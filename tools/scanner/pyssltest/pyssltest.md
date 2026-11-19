---
name: "pyssltest"
categories:
  - blackarch-scanner
version: "9.d7703f0"
homepage: "https://github.com/moheshmohan/pyssltest"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "moheshmohan/pyssltest"
github-stars: 42
github-pushed: "2017-10-03"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pyssltest

> pyssltest — batch-drives the Qualys SSL Labs API over a file of URLs and collects per-host SSL assessment results into a CSV.

## What it's for

A Python 2, multithreaded client for the public Qualys SSL Labs server-testing API: it reads a list of URLs, submits each for assessment, tracks the jobs, and writes the outcomes to a CSV. It is built for checking a whole portfolio of external hostnames in one run rather than one site at a time. Because the assessment happens server-side at Qualys against internet-reachable hosts, it does not test internal-only names, and results are only as current as the SSL Labs API and its cache.

## Typical use cases

### Assess a batch of hosts

Grade every URL in a file in one run.

```
python2 pyssltest.py -i urls.txt -o results.csv
```

Outcome: each URL from `urls.txt` is submitted to the SSL Labs API and tracked to completion, then per-host outcomes are written to `results.csv` — both the input and output arguments are required.

### Force fresh assessments

Bypass cached results from earlier scans.

```
python2 pyssltest.py -i urls.txt -o results.csv -n
```

Outcome: a new assessment is requested per host (`-n`) so SSL Labs re-runs the full scan instead of returning a cached report — slower, but current as of this run.

*No corpus evidence — derived from upstream source (`pyssltest.py`; `-i`/`-o` required and `-n` flag verified in its argparse).*
