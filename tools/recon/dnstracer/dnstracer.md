---
name: "dnstracer"
categories:
  - blackarch-recon
version: "1.10"
homepage: "https://www.mavetju.org/unix/dnstracer.php"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2024-12-06"
build-system: make
license: custom:BSD
meta-checked: "2026-11-09"
worth: 1.5
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# dnstracer

> Traces which DNS servers a resolver consults for a name, printing the delegation chain from the root servers down to the authoritative answer.

## What it's for

dnstracer (by Edwin Groothuis, distributed from mavetju.org) answers the troubleshooting question "where does this DNS server get its answer from?". Given a hostname it recursively asks the server which next server to query, following the chain from the root servers through the TLD servers to the zone's authoritative servers, and marks which replies were authoritative and which came from cache. Use it to debug delegation problems, caching behaviour and suspected poisoning; it does not enumerate or bruteforce subdomains.

## Typical use cases

### Trace a name through the default resolver

```
dnstracer www.example.com
```

Outcome: prints a tree starting at your system resolver, branching through the root and gTLD servers to the authoritative servers for the name, annotating each hop with "Got authoritative answer" or "(cached)".

### Start at a specific server with an end-of-run overview

```
dnstracer -o -s 198.51.100.10 www.example.com
```

Outcome: traces only from the given starting server (`-s` takes a name, an IP, or `.` for the A root server), and `-o` adds an overview of the answers each authoritative server returned at the end — keeps output readable on verbose zones.

### Trace a reverse (PTR) record

```
dnstracer -q ptr 10.100.51.198.in-addr.arpa
```

Outcome: follows the delegation chain for the reverse zone and shows which server ultimately answers the PTR query; reverse names must be given in `in-addr.arpa` form.

### Trace the SOA record with zone details

```
dnstracer -q soa -o example.com
```

Outcome: the final overview includes the zone's serial number, mname and rname fields, making stale serials and a wrong primary server immediately visible.

*No corpus evidence — derived from upstream documentation.*
