---
name: "magescan"
categories:
  - blackarch-webapp
version: "1.12.9"
homepage: "https://github.com/steverobbins/magescan"
packaged-by: [blackarch]
blackarch-builddate: "2019-02-16"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "steverobbins/magescan"
github-stars: 461
github-pushed: "2020-04-02"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# magescan

> Black-box scanner that profiles the security and quality of a Magento store.

## What it's for

Mage Scan (by Steve Robbins, shipped as a PHP .phar) evaluates a Magento e-commerce site from the outside, without any credentials or code access: it guesses the Magento version, lists detectable installed modules, reads catalog statistics, checks the sitemap, identifies server technology, reports patch availability, and tests which well-known paths are reachable or unreachable. The intended scenario is vetting a store you cannot log into — for example when assessing a potential client or developer's work. All findings are best-effort inference from public responses; the tool itself says information reported is not guaranteed to be correct.

## Typical use cases

### Run the full scan

```
magescan scan:all https://store.example.com
```

Outcome: prints a consolidated report — version, modules found, sitemap status, server tech, catalog info, patch and path checks — for the store. Add `-k` to skip TLS certificate validation against self-signed stores. (The package wraps `php /usr/share/magescan/magescan.phar`; using the release phar directly, the same run is `php magescan.phar scan:all https://store.example.com`.)

### Emit JSON output

```
magescan scan:all --format=json https://store.example.com > magescan.json
```

Outcome: the same full scan, emitted as JSON for diffing against a later run or feeding a report pipeline.

### Fingerprint the Magento version only

```
magescan scan:version https://store.example.com
```

Outcome: prints the inferred Magento version — the fastest check to decide whether known Magento CVEs plausibly apply before deeper testing.

### Enumerate installed modules

```
magescan scan:modules --show-modules https://store.example.com
```

Outcome: lists the third-party modules whose fingerprints were detected; `--show-modules` additionally lists every module signature searched for, not only the hits.

Provenance: upstream README; the `scan:all` whole-store pattern is field-verified usage of the phar release.
