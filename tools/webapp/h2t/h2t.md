---
name: "h2t"
categories:
  - blackarch-webapp
version: "38.56e1447"
homepage: "https://github.com/gildasio/h2t"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "gildasio/h2t"
github-stars: 412
github-pushed: "2026-01-07"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# h2t

> HTTP Hardening Tool: audits a site's response headers and recommends security headers to add or drop.

## What it's for

h2t (the packaged `h2t` command wraps h2t.py) fetches a URL's HTTP response headers and grades them against a built-in catalog of "good" headers (security hardening: HSTS, CSP and friends) and "bad" ones (information disclosure), printing concrete recommendations for the sysadmin. It is a quick pre-assessment or hygiene check: point it at a URL, get the header gaps. It only inspects response headers on URLs you give it — it is not a vulnerability scanner and never sends offensive payloads.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Scan a site's headers for hardening gaps

One command for the full picture.

```
h2t scan https://example.com
```

Outcome: prints which good headers are missing and which bad headers are present, each with an explanation of the finding (add `-E` to silence the explanation block).

### Check only good or only bad headers

Focus the audit on one direction.

```
h2t scan -g -p description refs https://example.com
```

Outcome: restricts the check to recommended security headers (`-g`; `-b` is the bad-only counterpart) and prints each finding with a description and reference links from the catalog.

### Machine-readable output for tracking

Keep deltas between audits in a file.

```
h2t scan -o json https://example.com > headers.json
```

Outcome: writes the scan results as JSON to `headers.json` (`-o csv` is also available) so successive audits can be diffed.

### Browse the header catalog

See what h2t knows before narrowing a scan.

```
h2t list -p description refs
```

Outcome: lists every header in the catalog with a description and further-reading links — the vocabulary for the scan subcommand's `-H` selection.
