---
name: "fingerprinter"
categories:
  - blackarch-webapp
version: "486.e0078b4"
homepage: "https://github.com/erwanlr/Fingerprinter"
packaged-by: [blackarch]
blackarch-builddate: "2025-09-17"
build-system: ruby
license: CC-BY-NC-4.0
meta-checked: "2026-11-09"
github-repo: "erwanlr/Fingerprinter"
github-stars: 256
github-pushed: "2025-09-17"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fingerprinter

> Determines the exact version of CMS/LMS/library web applications from per-file fingerprint databases.

## What it's for

Fingerprinter (by erwanlr, Ruby) pins down the precise version of a remote web application — WordPress, Drupal, Joomla, Moodle, phpMyadmin, PrestaShop, Chamilo LMS and roughly twenty others — by requesting known versioned files and comparing their checksums against a bundled per-app database of fingerprints. Knowing the exact version narrows the search for matching CVEs and public exploits, especially when generator tags and footers are scrubbed. It complements rather than replaces vulnerability scanners: it identifies versions, it does not test for flaws. Licensed CC BY-NC 4.0 — non-commercial use only.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Fingerprint with the full database

Identify a target's version using every known file hash.

```
fingerprinter -a wordpress --fingerprint http://target.example.com/blog/
```

Outcome: requests known versioned files under the target and reports the WordPress version(s) whose fingerprints match, along with the files that matched.

### Use only unique fingerprints

Faster and usually more reliable identification.

```
fingerprinter -a wordpress --unique-fingerprint http://target.example.com/blog/
```

Outcome: tests only fingerprints unique to a single version across the database — quicker and less ambiguous, at the risk that some versions ship no uniquely-hashable files.

### Fingerprint passively

Identify a version with minimal footprint.

```
fingerprinter -a wordpress --passive-fingerprint http://target.example.com/blog/
```

Outcome: only resources the homepage already references (JavaScript files, images, stylesheets) are fetched and hashed against the database — no probing of extra paths.

### Query the local fingerprint database

Plan or confirm identifications offline.

```
fingerprinter -a wordpress --search-file readme%
```

Outcome: reports which WordPress versions ship files matching the pattern — `--search-hash` does the reverse lookup from a single known file hash, and `--list-unique-fingerprints 3.8.1` enumerates a version's unique hashes.
