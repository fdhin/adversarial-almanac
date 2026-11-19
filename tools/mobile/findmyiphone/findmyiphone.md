---
name: "findmyiphone"
categories:
  - blackarch-mobile
version: "19.aef3ac8"
homepage: "https://github.com/manwhoami/findmyiphone"
packaged-by: [blackarch]
blackarch-builddate: "2017-03-30"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "manwhoami/findmyiphone"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# findmyiphone

> Enumerated every device tied to an iCloud account through Apple's Find My iPhone service.

## What it's for

A small Python 2 reconnaissance script (author `manwhoami`) that, given access
to an iCloud session, queried Apple's Find My iPhone web service and located
all devices associated with the account — the class of demonstration used to
show what a leaked iCloud credential or session exposes. The upstream
repository has been deleted and no archive or fork survives (the Wayback CDX
index holds only a 404 capture), so the exact command-line interface can no
longer be verified — hence this note documents purpose only. For maintained
clients of the same service, see pyicloud's Find My iPhone module or other
fmipservice clients.

*No corpus evidence — derived from upstream documentation.*
