---
name: "dnsbf"
categories:
  - blackarch-scanner
version: "0.3"
homepage: "https://code.google.com/archive/p/dnsbf/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-14"
build-system: scripts
license: BSD-3-Clause
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# dnsbf

> Multithreaded reverse-DNS sweep of an IP range: list which addresses have hostnames.

## What it's for

dnsbf (by t0ka7a, infond.blogspot.com) takes a CIDR block and does a
reverse lookup (`gethostbyaddr`) on every address, up to 200 concurrent
threads. Its output is the PTR map of a subnet: which IPs carry DNS names
and which are nameless — the nameless ones being the pool where free
(unclaimed) hostnames can be hunted in bulk. It is a one-argument Python 2
script with no options; point DNS at a resolver that will answer reverse
queries for the range.

## Typical use cases

### Sweep a subnet's reverse DNS

Map hostnames across an address block.

```
dnsbf 198.51.100.0/24
```

Outcome: prints one `<ip> <hostname>` line per address that resolves,
followed by `end of search` and a summary line
`<N> ip tested, <M> names found, in <X> s`; addresses absent from the
output have no PTR record — redirect to a file to build the range's
name/no-name inventory.

*No corpus evidence — derived from the packaged source (dnsbf.py 0.3 from
the archived Google Code project).*
