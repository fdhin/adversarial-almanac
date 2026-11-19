---
name: "blindelephant"
categories:
  - blackarch-fingerprint
version: "8"
homepage: "https://blindelephant.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-14"
build-system: scripts
license: LGPL-3.0-or-later
aliases: ["blindelephant-svn"]
meta-checked: "2026-11-09"
github-repo: "lokifer/BlindElephant"
github-stars: 80
github-pushed: "2020-09-02"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# blindelephant

> Web application version fingerprinter that compares static files at known paths against precomputed per-release hashes to pin down exactly which version of a known web app is deployed.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Determining the precise version of a known web application (WordPress, Drupal, Movable Type, phpBB, and dozens more) during reconnaissance. BlindElephant (by Patrick Thomas, Qualys, presented at BlackHat 2010) fetches a small number of static files (JavaScript, CSS, docs) whose content differs between releases, hashes them, and compares the results against a precomputed database of version-differentiating file hashes — fast, low-bandwidth and non-invasive. It only works for applications and plugins that ship a signature database (use `guess` as the app name when you don't know which supported app the site runs); it cannot fingerprint custom applications. Development stopped around 2011 and the tool is Python 2, so releases newer than its databases are invisible to it.

## Typical use cases

### Fingerprint a known web application's version

Check which release of an already-identified application is deployed.

```
blindelephant http://target.example.com wordpress
```

Outcome: loads the wordpress signature database, fetches differentiating files (15 by default, tune with `-n`), narrows the candidate set per file fetched, and ends with `Fingerprinting resulted in:` followed by the most likely version(s); `-w` applies winnowing to narrow ambiguous results further.

### Identify which supported application is installed

Discover which of the supported web apps the site actually runs.

```
blindelephant http://target.example.com guess
```

Outcome: probes the site against the databases of all supported web apps and reports which one(s) appear to be installed, giving you the app name to re-run with for exact versioning.

### Fingerprint a plugin instead of the application

Version-check a specific plugin of a supported application.

```
blindelephant -p akismet -s http://target.example.com wordpress
```

Outcome: skips the web-app check (`-s`) and fingerprints the named plugin using its own differentiating files, printing candidate plugin versions the same way.

### List supported applications and plugins

See what this tool can recognize before running it.

```
blindelephant -l
```

Outcome: prints every web app and plugin for which a signature database ships, giving the valid names for the appName and `-p` arguments.
