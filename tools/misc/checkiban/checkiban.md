---
name: "checkiban"
categories:
  - blackarch-misc
version: "0.2"
homepage: "http://kernel.embedromix.ro/us/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: make
license: LGPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# checkiban

> Command-line checksum validator for International Bank Account Numbers (IBAN).

*No corpus evidence — derived from upstream documentation.*

## What it's for

checkIBAN is a small C utility (by Catalin BOIE, 2005) that answers one question:
does this IBAN pass its checksum? It strips non-alphanumeric characters, moves the
first four characters to the end, converts letters to their numeric equivalents and
computes the ISO 13616 modulo-97 remainder with a bundled big-number library — a
valid IBAN lands on 1. It validates structure only: it cannot confirm that an
account exists and does not look up banks, countries or format lengths.

## Typical use cases

### Validate an IBAN checksum

Check whether a candidate IBAN is structurally valid.

```
checkiban "GB82WEST12345698765432"
```

Outcome: prints each derivation step (cleaned input, rearranged string, converted
number, remainder) and ends with `OK!` for a valid IBAN or `NOT OK!` otherwise.
The exit status is 0 in both cases, so script on the printed verdict rather than
the return code; with no argument it prints the usage line and exits 1.
