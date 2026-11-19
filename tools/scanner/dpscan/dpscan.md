---
name: "dpscan"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "https://github.com/insaneisnotfree/Blue-Sky-Information-Security"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "insaneisnotfree/Blue-Sky-Information-Security"
github-stars: 14
github-pushed: "2012-02-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dpscan

> Fetches a Drupal site and prints the names of its enabled modules — recon input for version-driven module research.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Narrowing the attack surface of a Drupal installation. Despite the "scanner" in its package description, this 2012-era script (by Ali Elouafiq) is an enumerator: it retrieves the target page with `wget` (with optional HTTP Basic credentials), extracts every `.../modules/...` path reference from the markup, and prints the deduplicated module directory names. Those names are the starting point for the real work — looking up each module's advisory history by hand — so the value is the list, not a verdict. It performs no version fingerprinting, no exploitation, and no vulnerability decision of its own, and it needs `wget` on PATH since it shells out to it.

## Typical use cases

### List enabled modules on a Drupal site

```
dpscan http://target.example.com/
```

Outcome: fetches the page, filters module paths out of the returned markup and prints one module directory name per line, deduplicated; feed each name into module advisory lookups to spot known-vulnerable components.

### Enumerate modules behind HTTP Basic authentication

```
dpscan http://target.example.com/ user Password1
```

Outcome: passes the supplied credentials to `wget` as `--http-user`/`--http-password` and performs the same module extraction on the protected page.
