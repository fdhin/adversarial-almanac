---
name: "drupalscan"
categories:
  - blackarch-webapp
version: "0.5.2"
homepage: "https://rubygems.org/gems/DrupalScan/"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: ruby
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "rverton/DrupalScan"
github-stars: 15
github-pushed: "2012-10-23"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# drupalscan

> Simple non-intrusive Drupal scanner.

## What it's for

DrupalScan is a small Ruby scanner that enumerates the modules of a Drupal site and identifies its version, fetching each found module's description from drupal.org so the report is annotated with what every module does. It is non-intrusive — plain GET probing of module paths and version markers, no exploitation. Reach for it when you want a one-shot, dependency-light module+version read of an old Drupal site (it targets the Drupal 6/7 era and the upstream has been archived since 2012, so modern Drupal 9/10 sites will under-report). It is also usable as a Ruby library (`DrupalScan.enumerateModules`, `DrupalScan.getVersion`). The gem installs the command as `DrupalScan`.

## Typical use cases

### Enumerate modules and version of a Drupal site

The canonical command-line run.

```
DrupalScan -u http://target.example.com
```

Outcome: prints the detected Drupal version line (e.g. `Version: Drupal 6.26, 2012-05-02`) followed by every enumerated module with the description pulled from drupal.org — a self-annotated module inventory for CVE matching.

### Enumerate without drupal.org lookups

Skip the description lookups for speed or offline runs.

```
DrupalScan -u http://target.example.com --no-module-lookup
```

Outcome: reports the version and module names only, without fetching each module's description from drupal.org (`-n` is the short form).

### Use it from Ruby

Embed the enumeration in a Ruby script.

```
ruby -r DrupalScan -e 'puts DrupalScan::getVersion("http://target.example.com"); puts DrupalScan::enumerateModules("http://target.example.com")'
```

Outcome: returns the version string and the module list as Ruby values instead of printed text, for scripting the enumeration into a larger recon pipeline.

No corpus evidence — derived from upstream documentation (README usage text; gem-installed `DrupalScan` executable).
