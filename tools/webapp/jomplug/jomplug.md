---
name: "jomplug"
categories:
  - blackarch-webapp
version: "0.1"
homepage: "http://packetstormsecurity.com/files/121390/Janissaries-Joomla-Fingerprint-Tool.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# jomplug

> PHP script that fingerprints Joomla components across a site list and matches them against Packet Storm advisories.

## What it's for

jomplug (the Janissaries "Joomla Fingerprint Tool" by Miyachung) is a single PHP script for batch Joomla reconnaissance: for each site in a list it enumerates the installed Joomla components, then queries Packet Storm's search archive for advisories and exploits mentioning each component name. It is multithreaded (five sites at a time via curl multi handles) and needs only the `php` CLI. It reports potentially vulnerable components — it does not test or exploit anything itself. Expect 2013-era results: modern Joomla sites hide component signatures far better than the ones this script greps for, and its advisory matching is only as good as Packet Storm's search page.

No corpus evidence — derived from upstream documentation (packaged source).

## Typical use cases

### Fingerprint and match a batch of Joomla sites

Feed it a file of sites and read the advisory matches.

```
jomplug sites.txt
```

Outcome: each site (one `www.example.com`-style hostname per line) is fingerprinted for Joomla components; for every component found, matching Packet Storm advisories and exploit titles are printed under the site's name, five sites processed concurrently.
