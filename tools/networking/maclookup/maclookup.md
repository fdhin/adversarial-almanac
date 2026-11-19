---
name: "maclookup"
categories:
  - blackarch-networking
version: "0.4"
homepage: "https://github.com/paraxor/maclookup"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "paraxor/maclookup"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# maclookup

> Shell script that resolves MAC addresses to their registered vendor using the IEEE MA-L/OUI listing.

## What it's for

maclookup (by paraxor) is a small shell utility that maps MAC addresses to the organization that registered their OUI prefix, using the IEEE public MA-L/OUI registry as its data source. The packaged script installs as `/usr/bin/maclookup` and works against a local copy of the IEEE `oui.txt` database kept at `/usr/share/maclookup/oui.txt`, so lookups run offline after the database is populated. Its value is fingerprinting network gear from packet captures, ARP tables and DHCP logs — turning raw hardware addresses into "Cisco", "Apple", "Raspberry Pi Trading" style vendor names. The upstream GitHub repository has been deleted, and the source is no longer archived anywhere public, so only the interface exercised by the packaging recipe (database update flags `-u` and `-f`) remains verifiable today.

## Typical use cases

### Refresh the local OUI database

```
sudo maclookup -u -f /usr/share/maclookup/oui.txt
```

Outcome: downloads the current IEEE MA-L/OUI public listing and rewrites the local oui.txt database — the exact invocation the packaging recipe uses to build the shipped copy, and the way to bring an old install up to date with newly registered prefixes. Run this before lookups if the database is stale or missing.

*No corpus evidence — derived from upstream documentation.*
