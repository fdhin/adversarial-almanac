---
name: "malheur"
categories:
  - blackarch-forensic
version: "0.5.4"
homepage: "http://www.mlsec.org/malheur/"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-24"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "rieck/malheur"
github-stars: 377
github-pushed: "2019-05-08"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# malheur

> Automatic analysis of malware behavior (Konrad Rieck): prototype extraction, clustering, and classification of sandbox behavior reports.

## What it's for

Malheur applies machine learning to recorded malware behavior: it takes directories of behavior reports — instruction-level MIST traces exported from sandbox tools such as CWSandbox — and supports four analysis actions: extracting a representative set of prototypes from a report collection, clustering reports into behaviorally similar groups (novel malware classes), classifying unknown reports against previously established groups, and incremental processing so large or daily feeds stay tractable in time and memory. It accompanied the 2011 Journal of Computer Security paper "Automatic Analysis of Malware Behavior using Machine Learning" (Rieck, Trinius, Willems, Holz). It does not run malware itself — it operates on behavior reports you have already collected. Development is dormant (last release 0.5.4, 2015-era); treat it as research software.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Cluster a set of behavior reports

```
malheur cluster /data/sandbox-reports
```

Outcome: groups the reports into clusters of similar behavior, printing cluster assignments and summary statistics; use it to discover novel behavioral classes in a sample set.

### Extract prototypes from a large report set

```
malheur prototype -o prototypes.txt /data/sandbox-reports
```

Outcome: selects a small representative subset of reports and writes it to prototypes.txt — a quick overview of recorded behavior to guide manual inspection.

### Classify unknown reports against known groups

```
malheur classify /data/new-reports
```

Outcome: assigns each new report to the known group closest in behavior, triaging unknown samples into previously analyzed families; run it after clustering has established the reference state.

### Process a large feed incrementally

```
malheur increment /data/daily-feed
```

Outcome: processes the reports in chunks and updates internal state per run, keeping memory and run-time bounded for long-term daily analysis.
