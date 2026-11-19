---
name: "rpak"
categories:
  - blackarch-windows
version: "1.0"
homepage: "http://ntsecurity.nu/toolbox/rpak/"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-28"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rpak

> Routing Protocol Attack Kit: Windows console tools to sniff and inject RIP, RIP-2, IGRP and OSPF traffic (Arne Vidstrom, 2002).

## What it's for

RPAK is a 2002 collection of seven small Windows console programs by
Arne Vidstrom (ntsecurity.nu) for attacking routing protocols from a
host sitting on a routed segment. The spoofers replay a routing-table
file as forged RIP, RIP-2 or IGRP updates — injecting or poisoning
routes on protocols that authenticate weakly or not at all — while the
sniffers passively log live routing updates, including the plaintext
passwords carried by RIP-2 and OSPF. Each tool prints its own usage when
run. They are period tools for Windows 2000/XP/2003/Vista; modern
networks rarely still run RIPv1/IGRP, but where legacy protocols survive
these remain the quickest way to demonstrate route injection from a
compromised Windows host. BlackArch ships the binaries under
`/usr/share/windows/rpak/`; the original download is no longer hosted on
the author's site (archived copies survive).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Inject fake routes with forged RIP updates

Advertise an arbitrary routing table to RIP v1 speakers.

```
ripspoof routes.txt 198.51.100.10
```

Outcome: continuously sends RIP v1 updates built from `routes.txt`,
sourced from the given IP — neighbors that trust RIP install the
advertised routes; run the tool with no arguments to see the expected
routing-table file format.

### Poison an authenticated RIP-2 domain

Replay updates that carry the domain's password.

```
rip2spoof routes.txt 198.51.100.10 secret
```

Outcome: sends the routing table from `routes.txt` as RIP-2 updates
authenticated with the password `secret` — first capture the password
with `rip2sniff`, then redirect traffic by injecting a better-metric
route.

### Hijack routes in an IGRP autonomous system

Inject updates into a Cisco IGRP domain.

```
igrpspoof routes.txt 100 198.51.100.10
```

Outcome: transmits IGRP updates for autonomous system 100 built from
`routes.txt` from the spoofed source address, causing IGRP routers on
the segment to recalculate their routes around the entries you supply.

### Capture routing updates and their passwords

Passively harvest protocol traffic, including plaintext secrets.

```
rip2sniff
```

Outcome: logs every RIP-2 packet seen on the segment to the console,
including the authentication type and plaintext password; `ospfsniff`
does the same for OSPF ("Sniffing OSPF passwords...") and `ripsniff` /
`igrpsniff` log their protocols' updates.
