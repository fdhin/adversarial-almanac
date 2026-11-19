---
name: "drupal-module-enum"
categories:
  - blackarch-webapp
version: "11.525543c"
homepage: "https://github.com/Tethik/drupal-module-enumeration"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Tethik/drupal-module-enumeration"
github-stars: 4
github-pushed: "2019-01-20"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# drupal-module-enum

> Enumerate on drupal modules.

## What it's for

A minimal Drupal module enumerator: it probes a Drupal site for installed modules by requesting module paths from a bundled wordlist (`drupal-modules.lst`, a few thousand known module names) and reporting which ones exist. Reach for it when you only need the module inventory of a Drupal install and nothing else; it does not detect versions, themes, users or CVEs, and it has no options beyond the target URL. The upstream repository is archived and the script is Python 2, and the author himself now points users at `nmap`'s `http-drupal-enum` NSE script as the maintained alternative — treat this as a historical tool that still runs the one job it has. The BlackArch package wraps the script as `/usr/bin/drupal-module-enum` with the wordlist at `/usr/share/drupal-module-enum/`.

## Typical use cases

### Enumerate installed modules on a Drupal site

The single supported invocation: a target URL.

```
drupal-module-enum http://target.example.com
```

Outcome: walks the bundled `drupal-modules.lst` wordlist against the target, printing each module path that returns a hit — the module inventory you then match against known vulnerable Drupal modules.

No corpus evidence — derived from upstream documentation and source (README usage line and the BlackArch wrapper script).
