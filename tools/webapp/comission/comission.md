---
name: "comission"
categories:
  - blackarch-webapp
version: "v1.2.0.r14.g67b890e"
homepage: "https://github.com/Intrinsec/comission"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Intrinsec/comission"
github-stars: 68
github-pushed: "2023-05-22"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# comission

> White-box integrity audit of a CMS installation: diffs core, plugins and themes against fresh upstream archives.

## What it's for

CoMisSion analyzes a CMS you have on disk (WordPress or Drupal): it determines core/plugin/theme versions, downloads the matching pristine archives from the CMS's official site, and reports every addition, alteration and deletion relative to that baseline — catching backdoors, dropped webshells and undocumented "hotfix" edits that black-box scanners cannot see. It also maps the found versions against public vulnerability databases (WordPress, via the wpvulndb API with a token). It does not do source-code vulnerability analysis; version lookups only. Reports come in XLSX, CSV, JSON or stdout, which makes it easy to slot into a CI pipeline or a host-review checklist.

## Typical use cases

### Audit a WordPress tree and get a spreadsheet

Full integrity check of core, plugins and themes.

```
comission -c wordpress -d /srv/www/target -o report.xlsx -t XLSX
```

Outcome: downloads fresh WordPress core plus every detected plugin/theme archive, diffs them against `/srv/www/target`, and writes `report.xlsx` with per-component version, drift (added/modified/deleted files) and any wpvulndb matches — pass `--wpvulndb-token <token>` to include the vulnerability lookups.

### Fast Drupal integrity check without vulnerability lookups

Core-only diff for a Drupal tree (Drupal has no vuln checks).

```
comission -c drupal -d /srv/www/target --skip-plugins --skip-themes -t STDOUT
```

Outcome: checks only Drupal core, prints the modification report to the terminal, and finishes without touching plugin/theme directories.

### JSON output for automation

Machine-readable results for a pipeline.

```
comission -c wordpress -d /srv/www/target -t JSON -o report.json --no-color
```

Outcome: writes the same findings as structured JSON to `report.json` (`--no-color` keeps the log stream clean for non-terminal capture), suitable for diffing between runs in CI.

No corpus evidence — derived from upstream documentation.
