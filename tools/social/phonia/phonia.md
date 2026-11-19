---
name: "phonia"
categories:
  - blackarch-social
version: "593.8ae14ff"
homepage: "https://github.com/entynetproject/phonia"
packaged-by: [blackarch]
blackarch-builddate: "2020-08-29"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "entynetproject/phonia"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# phonia

> Advanced toolkits to scan phone numbers using only free resources.

## What it's for

Phonia Toolkit is a phone number scanner in the PhoneInfoga lineage that
resolves standard facts about an international number — country, area,
carrier, line type — using only free resources, then layers OSINT
scanners (notably `footprints`) on top to surface what the number exposes
across public sources. It accepts a single number, a file of numbers, or a
custom-format reconnaissance mode that derives and tries multiple formats
from the number's country. The upstream repository
(entynetproject/phonia) is gone from GitHub; the upstream README survives
in the Internet Archive (snapshot of 2020-07) and matches the interface
documented here. It is a lead-generation aid, not a phone tracker.

## Typical use cases

### Scan a single phone number

Structured recon of one international number.

```
phonia -p 15554443333
```

Outcome: Prints the number's standard information (country, area, carrier,
line type) followed by the results of the default scanners.

### Scan a list of numbers into a file

Process a batch of leads and keep the output.

```
phonia -i input.txt -o output.txt
```

Outcome: Scans each number in the input file (one per line) and writes the
combined results to the output path.

### Run one scanner only

Footprint a number without the full scanner set.

```
phonia -p 15554443333 -s footprints
```

Outcome: Runs only the named scanner against the number and prints its
findings.

### Custom-format reconnaissance

Try derived number formats when direct scans find nothing.

```
phonia --recon -p 15554443333
```

Outcome: Generates custom formats based on the number's country code and
checks each, reporting which formats yield results.

*No corpus evidence — derived from upstream documentation (repo gone;
interface verified against the archived project README).*
