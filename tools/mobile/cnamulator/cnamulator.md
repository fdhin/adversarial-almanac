---
name: "cnamulator"
categories:
  - blackarch-mobile
version: "5.4667c68"
homepage: "https://github.com/packetassailant/cnamulator"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-26"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "packetassailant/cnamulator"
github-stars: 12
github-pushed: "2014-08-15"
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

# cnamulator

> Resolve the caller-ID name (CNAM) behind phone numbers, singly or in bulk, via the OpenCNAM API.

## What it's for

cnamulator is a small Go utility that queries the OpenCNAM API to resolve the CNAM ("caller name") of a phone number or a whole file of them, returning the owning organization for each — useful in black-box assessments to attribute phone numbers harvested from public documents (PDFs, contact pages) to organizations. It needs an OpenCNAM account's sid and token, accepts either `-phone` or `-file`, and prints a JSON `results` array of number/name pairs. Written in 2014 and labelled alpha by its author (Chris Patten, packetassailant); treat it as a thin, stable wrapper around the API rather than a maintained product.

## Typical use cases

### Look up a single number

```
cnamulator -phone=12125550123 -sid=<opencnam-sid> -token=<opencnam-token>
```

Outcome: prints a JSON object whose `results` array pairs the queried number with its CNAM (owning organization), or an empty name when the API has no record.

### Resolve a list of numbers

```
cnamulator -file=numbers.txt -sid=<opencnam-sid> -token=<opencnam-token>
```

Outcome: queries every number in the file (one per line) and prints a single JSON `results` array pairing each number with its resolved name, ready to grep or import for attribution.

*No corpus evidence — derived from upstream documentation.*
