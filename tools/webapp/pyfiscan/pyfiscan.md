---
name: "pyfiscan"
categories:
  - blackarch-webapp
version: "3009.358d56a"
homepage: "https://github.com/fgeek/pyfiscan"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-28"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "fgeek/pyfiscan"
github-stars: 574
github-pushed: "2026-09-26"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
---

# pyfiscan

> Local-filesystem scanner that finds outdated and end-of-life web applications (WordPress, Joomla, MediaWiki, ...) via YAML fingerprints.

## What it's for

The hosting-provider problem: knowing which user installations on a shared server run outdated web apps. It walks directories on disk (no HTTP requests), fingerprints installed applications — 80+ supported, from WordPress and Joomla to phpMyAdmin, Roundcube and MediaWiki — against YAML-defined fingerprints, and flags out-of-date and end-of-life versions, including CVE context and upgrade pointers. It also knows EOL branches (e.g. Drupal 6, MediaWiki < 1.37) and can generate email alerts from templates. GNU/Linux only; it is a version scanner, not a vulnerability exploiter — findings tell you what to upgrade, not what is provably exploitable. Fingerprints are plain YAML, so adding applications or tightening checks does not require code changes.

## Typical use cases

### Recursively scan a web root

Audit an entire vhost tree in one pass.

```
pyfiscan -r /var/www
```

Outcome: walks the directory recursively, fingerprints every recognized application and prints located outdated versions; without `-r`, a set of predefined per-user site directories is scanned instead (`--home` overrides where those home directories live).

### Scan for specific applications only

Cut noise on servers with a known application mix.

```
pyfiscan -r /home -a wordpress,joomla
```

Outcome: limits the run to the named applications (comma-separated `-a`), so only WordPress and Joomla installations are fingerprinted and reported — faster and easier to triage than the full 80+ application set.

### Scan from a file list

Reuse existing inventory (e.g. `locate` output) instead of walking the tree.

```
locate wordpress | pyfiscan --file /dev/stdin
```

Outcome: fingerprints the paths supplied by the file (one per line) rather than crawling, avoiding repeated full-filesystem I/O; logging detail follows `-l info|debug` and `pyfiscan.log` captures the run.

### Recheck previous results

Turn last month's CSV into a fresh audit.

```
pyfiscan --check previous-run.csv
```

Outcome: revalidates the entries recorded in an old CSV export and reports which findings remain (still outdated) versus fixed — the basis for tracking remediation across recurring scans.

*Provenance: no corpus evidence — derived from upstream documentation (README + `pyfiscan.py` docopt usage, verified 2026-11).*
