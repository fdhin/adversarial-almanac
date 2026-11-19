---
name: "dorkme"
categories:
  - blackarch-scanner
version: "57.0a7017a"
homepage: "https://github.com/blueudp/DorkMe"
packaged-by: [blackarch]
blackarch-builddate: "2023-05-04"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "blueudp/DorkMe"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# dorkme

> Runs curated Google-dork categories against a single site and reports which dorks hit — a first-pass hunt for exposed files and injectable endpoints.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Making dork-based discovery reproducible one target at a time. DorkMe (by Eduardo Pérez-Malumbres, "blueudp") ships a categorized dork collection — SQL injection, deprecated endpoints, information disclosure and similar files under its `dorks/` directory — and applies chosen categories against a URL you specify, saving positive matches into a `result/` folder. It answers "which classic exposures does this site have?" rather than proving exploitability: a dork hit is a lead for manual follow-up, not a finding. To avoid search-engine bans it deliberately throttles itself (roughly a minute between requests and a longer pause every hundred requests), so full runs take time; new dorks are added as dork/severity/description triples in the dork files, with your own kept in `mydorks.txt`. The upstream repository is gone — usage here is verified from an archived copy of its README, and the packaged launcher runs the original Python 2 script unmodified.

## Typical use cases

### Probe a site with one dork category

```
dorkme --url target.example.com --dorks vulns -v
```

Outcome: applies the dorks in the `vulns` category to the target with verbose output, saving positive matches under `result/`; this is the recommended first test because it finishes fastest.

### Sweep every dork category

```
dorkme --url target.example.com --dorks all -v
```

Outcome: runs the entire dork collection against the site (categories can also be combined, e.g. `--dorks Deprecated,Info`); expect a long, deliberately throttled run, and treat the accumulated matches as a checklist of exposures to verify by hand.
