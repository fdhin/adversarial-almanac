---
name: "pwnedornot"
categories:
  - blackarch-recon
version: "152.c388d26"
homepage: "https://github.com/thewhiteh4t/pwnedOrNot"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-28"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "thewhiteh4t/pwnedOrNot"
github-stars: 2674
github-pushed: "2026-03-28"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# pwnedornot

> Checks email addresses against Have I Been Pwned and then hunts public dumps for the matching plaintext password.

## What it's for

pwnedOrNot works in two phases: first it queries the HIBP v3 API for breaches containing the address, then it searches publicly accessible password dumps for that address and reports any plaintext password found. Whether a password surfaces depends entirely on dump availability — some breaches contain only emails. An HIBP API key is mandatory (free keys no longer exist for this endpoint class; purchase one from HIBP), supplied via `-k`, the `PWNED_API_KEY` environment variable, or `~/.config/pwnedornot/config.json`. Use only on addresses you are authorized to investigate.

## Typical use cases

### Check a single email address

Full two-phase check on one address.

```
pwnedornot -e user@example.com -k <api-key>
```

Outcome: prints the breach list for the address (name, date, verification status) and then any plaintext password found in public dumps; the key can equally come from `PWNED_API_KEY` or the config file, letting you drop `-k`.

### Check breach exposure without password hunting

Skip the dump-searching phase.

```
pwnedornot -e user@example.com -n
```

Outcome: reports only the HIBP breach information — faster and quieter when you just need exposure metadata, not credentials.

### Batch-check a file of addresses

Process a whole target list and keep the hits.

```
pwnedornot -f emails.txt -s pwned.txt
```

Outcome: checks every address in the file and writes the pwned ones (with their findings) to `pwned.txt`, giving a reusable output artifact for reporting.

### Filter results to one breach domain

Narrow a check to a specific service.

```
pwnedornot -e user@example.com -d example.com
```

Outcome: shows only breaches whose domain matches the given one (e.g. only the target organization's own incidents); works with `-f` batch runs too.

### Enumerate breached domains and test your own

Domain-centric views of the breach corpus.

```
pwnedornot -l
pwnedornot -c example.com
```

Outcome: `-l` lists all breached domains known to HIBP; `-c` answers whether the given domain appears in any breach, with `-b <name>` available for details on a single named breach.

No corpus evidence — derived from upstream documentation.
