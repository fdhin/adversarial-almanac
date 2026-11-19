---
name: "vbscan"
categories:
  - blackarch-webapp
version: "39.2b1ce48"
homepage: "https://github.com/rezasp/vbscan"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "rezasp/vbscan"
github-stars: 325
github-pushed: "2019-06-16"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vbscan

> OWASP VBScan — Perl scanner that detects and analyzes known vBulletin CMS vulnerabilities on a forum target.

## What it's for

OWASP VBScan (VBulletin Vulnerability Scanner, by Mohammad Reza Espargham / rezasp) is an open-source Perl project to detect vBulletin CMS vulnerabilities and analyze them: it fingerprints the target's vBulletin version and checks it against a bundled knowledge base of known vBulletin issues, reporting what applies with references. It is detection and analysis only — it does not attempt exploitation. Development stopped around 2019 (the repository is archived), so its vulnerability knowledge predates later vBulletin 5.x disclosures; verify any finding against current advisories before reporting. It was a ToolsWatch top-ten tool in its heyday.

## Typical use cases

### Scan a vBulletin forum

The single documented invocation.

```
vbscan http://target.example.com/vbulletin
```

Outcome: fingerprints the vBulletin installation and prints a sectioned report of matched known vulnerabilities with their references, ending with a summary of the issues found.

No corpus evidence — derived from upstream documentation (README of rezasp/vbscan, OWASP VBScan project).
