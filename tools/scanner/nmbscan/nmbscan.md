---
name: "nmbscan"
categories:
  - blackarch-scanner
version: "1.2.6"
homepage: "http://nmbscan.gbarbier.org/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# nmbscan

> Old-school LAN utility that maps the SMB/NetBIOS neighborhood — domains, master browsers, servers, MACs, server software and user candidates.

## What it's for

nmbscan interrogates the NMB/SMB/NetBIOS browse lists on a local network and reports, per host: NetBIOS/Windows hostname, IP address and DNS name, ethernet MAC address, domain, master browser, server software and operating system, plus the username a share advertises ("user-candidate"). It discovers hosts through the lists maintained by master browsers, so it sees machines that never answer a ping. It is a read-only inventory/audit helper from the Samba-2 era (upstream site is gone; the archived homepage documents usage) — for modern networks `nbtscan`/`crackmapexec` cover the same ground, but nmbscan's browse-list walk remains a fast way to draw the domain map on legacy segments.

## Typical use cases

### Map the whole SMB neighborhood

```
nmbscan -a
```

Outcome: prints every domain with its master browser and all member servers, each with IP, DNS name, MAC address, server software and OS as the browse lists report them.

### List domains or find the master browsers

```
nmbscan -d
nmbscan -m
```

Outcome: `-d` prints just the domain names visible on the segment; `-m` additionally shows which host is the master browser of each domain.

### Inspect specific hosts by address or name

```
nmbscan -h 198.51.100.10 198.51.100.20
nmbscan -n SERVER01
```

Outcome: `-h` looks hosts up by IP address or DNS name, `-n` by NetBIOS name, returning the same per-host detail block without walking the whole network.

*No corpus evidence — derived from upstream documentation (archived nmbscan.gbarbier.org homepage, usage block and sample output; packaged as `/usr/bin/nmbscan` by BlackArch).*
