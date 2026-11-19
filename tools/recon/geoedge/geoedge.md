---
name: "geoedge"
categories:
  - blackarch-recon
version: "0.2"
homepage: "http://www.edge-security.com/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# geoedge

> Tiny 2010-era Python 2 geolocator by Christian Martorella (edge-security.com): queries the MaxMind and Geoiptool web lookups for one host or IP and prints country, city, coordinates, provider and map links.

## What it's for

Geoedge is a minimal single-argument geolocation lookup: give it a hostname or IP and it scrapes two independent sources — the MaxMind online locator and Geoiptool — printing each source's country, city and decimal coordinates (Maxmind additionally yields the provider) along with ready-made Google Maps and MapQuest links for the coordinates. It was written around 2010 (v0.2, Python 2, HTTP-only) and hard-codes the web endpoints of that era, so it is primarily of historical and scripting-reference interest: expect the scraping to fail if those services have moved, and treat any response as a bonus rather than a guarantee. It performs lookups only — no scanning, no exploitation. The packaged source is the script distributed from the author's site; there is no canonical GitHub repository (the BlackArch PKGBUILD downloads `geoedge.py` directly from edge-security.com).

## Typical use cases

### Geolocate a host or IP

Resolve an address to geographic and provider details.

```
geoedge 198.51.100.10
```

Outcome: prints a Maxmind section (country, city, coordinates, provider, Google Maps/MapQuest links) followed by a Geoiptool section (country, city, coordinates) for the address; "Connection error" from either source means that endpoint no longer answers the way the script expects.

*No corpus evidence — derived from upstream documentation (packaged script fetched from edge-security.com and verified directly; the frontmatter's former GitHub link pointed at an unrelated same-named Flutter scaffold and was removed).*
