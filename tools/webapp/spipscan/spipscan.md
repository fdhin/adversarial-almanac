---
name: "spipscan"
categories:
  - blackarch-webapp
version: "69.4ad3235"
homepage: "https://github.com/PaulSec/SPIPScan"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "PaulSec/SPIPScan"
github-stars: 72
github-pushed: "2014-08-29"
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

# spipscan

> SPIP CMS scanner: version, plugin, theme, and sensitive-folder detection.

## What it's for

SPIPScan detects SPIP (the French CMS) installations during penetration testing. It
identifies the SPIP version, enumerates installed plugins and themes (either from the
plugin folder listing or by brute force against known plugin names), checks for sensitive
folders, flags possible known vulnerabilities, and can brute-force user logins. It only
targets SPIP — it is not a general-purpose CMS scanner — and its vulnerability data
reflects the 2014 era in which it was written.

## Typical use cases

### Detect the SPIP version

Fingerprint a target and read out its SPIP version.

```
spipscan -w http://target.example.com -V
```

Outcome: prints where the application is located and the detected SPIP version (e.g. `[!] Version is : 3.0.13`), plus the plugin folder name.

### Run a broad scan in one pass

Version, themes, plugins, and vulnerability hints together.

```
spipscan -w http://target.example.com -S
```

Outcome: `-S` is shorthand for the version, themes, plugins, and vulnerability checks in a single run; results are printed per check.

### Brute-force plugin names

Enumerate plugins when the plugin folder listing is not accessible.

```
spipscan -w http://target.example.com -p --bruteforce_plugins_file plugins_name.db
```

Outcome: requests `plugin.xml`/`paquet.xml` under the plugin folder for each candidate name from the given file and reports each existing plugin it finds. Analogous flags exist for themes (`--bruteforce_themes_file`) and logins (`--bruteforce_logins_file`).

No corpus evidence — derived from upstream documentation.
