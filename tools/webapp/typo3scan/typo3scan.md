---
name: "typo3scan"
categories:
  - blackarch-webapp
version: "v1.2.final.r0.g0c4ec73"
homepage: "https://github.com/whoot/Typo3Scan"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "whoot/Typo3Scan"
github-stars: 172
github-pushed: "2024-07-02"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# typo3scan

> Enumerates a site's TYPO3 CMS core version and installed extensions, then matches them against official TYPO3 security advisories.

## What it's for

Typo3Scan (by Jan Rude) automates the recon phase of a TYPO3 assessment: it detects the TYPO3 core version and the extensions installed on a target, then compares them against a bundled database of official TYPO3 security advisories and reports known-vulnerable components with their advisory references. It does not exploit anything — output is advisory matching for manual follow-up. Extension version detection is inherently heuristic (a generic regex over accessible files such as changelogs), so extension findings can be false positives and should be verified manually against the official extension repository. Version 1.2 is the final release: TYPO3 12+ (and composer-based installs) moved extension files out of reachable paths, and the tool no longer works against those.

## Typical use cases

### Enumerate TYPO3 core version and installed extensions

Get the inventory first.

```
typo3scan -d target.example.com
```

Outcome: fingerprints the TYPO3 core version and lists detected extensions with their guessed versions; results are printed to the console and also written to typo3scan.json in the working directory.

### Check enumerated components against security advisories

Turn the inventory into a vulnerability picture.

```
typo3scan -d target.example.com --vuln
```

Outcome: after enumeration, each identified core/extension version is matched against the stored official TYPO3 security advisories; known-vulnerable components are printed with the corresponding advisory details.

### Refresh the advisory database

Pull the latest advisory data before a scan.

```
typo3scan -u
```

Outcome: updates the tool's internal advisory/version database, then exits; subsequent scans match against the refreshed data.

No corpus evidence — derived from upstream documentation (README and typo3scan.py of whoot/Typo3Scan v1.2).
