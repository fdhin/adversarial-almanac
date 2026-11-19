---
name: "upnpscan"
categories:
  - blackarch-scanner
version: "0.4"
homepage: "http://www.cqure.net/wp/upnpscan/"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-06"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Seabreg/upnpscan"
github-stars: 0
github-pushed: "2019-03-22"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# upnpscan

> LAN scanner that finds UPnP-capable devices with SSDP M-SEARCH probes.

## What it's for

upnpscan (Patrik Karlsson, cqure.net) discovers Universal Plug and Play devices on a network by sending SSDP M-SEARCH discovery packets — either to the UPnP multicast group or unicast across an address range you specify — and reporting every host that answers. It is a small C/libpcap tool and version 0.4 is the final release from the 2008 era, so use it for quick enumeration of exposed UPnP endpoints on a segment and follow up with a modern SSDP/UPnP auditor for service-level inspection. The cqure.net site is gone; the Seabreg GitHub repository mirrors the original source tarball that BlackArch packages.

## Typical use cases

### Discover UPnP devices via multicast

```bash
upnpscan -m
```

Outcome: sends M-SEARCH to the UPnP multicast address and lists every device on the local segment that responds with an SSDP reply.

### Sweep an address range

```bash
upnpscan -t 198.51.100.1-20.*
```

Outcome: unicast M-SEARCH probes go to every host in the given range (here third octet 1-20, any host part) and UPnP-capable responders are reported.

*No corpus evidence — derived from upstream documentation (cqure.net project page, archived via the Wayback Machine, documenting `upnpscan -m` and `upnpscan -t <range>` usage; BlackArch PKGBUILD builds the v0.4 source tarball).*
