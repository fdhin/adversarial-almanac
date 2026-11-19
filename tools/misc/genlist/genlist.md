---
name: "genlist"
categories:
  - blackarch-misc
version: "0.1"
homepage: ""
packaged-by: [blackarch]
blackarch-builddate: "2017-01-14"
build-system: other
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# genlist

> Expands full or partial IPv4 ranges into exhaustive address lists.

## What it's for

A tiny C utility (Craig Cheetham, 2001) that prints every IPv4 address between a start and an end value, one per line — the essential step of building target lists for scanners. Its convenience is that start and end may be partial addresses, so an entire class-A, class-B or class-C block is expressed in a few keystrokes. The end address itself is excluded from the output. It only generates addresses; probing belongs to whatever it is piped into.

## Typical use cases

### Enumerate an explicit address range

```
genlist 198.51.100.1 198.51.100.5
```

Outcome: prints 198.51.100.1 through 198.51.100.4 (the end address is excluded), one address per line, ready to redirect into a file.

### Expand a partial address into a whole block

Omitted trailing octets expand to the full block.

```
genlist 198.51 198.52
```

Outcome: prints all 65536 addresses 198.51.0.0–198.51.255.255; the same shorthand works at other octet boundaries (e.g. `genlist 203.0.113 203.0.114` for a single class-C block).

### Feed a scanner directly

```
genlist 198.51.100.0 198.51.101.0 | nmap -iL - -sn
```

Outcome: the generated range streams into nmap as its target list and a ping sweep of those 256 hosts starts without an intermediate file.

*No corpus evidence — derived from upstream documentation.*
