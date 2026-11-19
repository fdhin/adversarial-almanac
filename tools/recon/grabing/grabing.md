---
name: "grabing"
categories:
  - blackarch-recon
version: "11.9c1aa6c"
homepage: "https://github.com/black-brain/graBing"
packaged-by: [blackarch]
blackarch-builddate: "2017-12-24"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "black-brain/graBing"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# grabing

> Counts all the hostnames indexed for an IP address.

## What it's for

graBing is a small Python 2 reconnaissance script (BlackArch installs it as
`grabing`, running `graBing.py`) that, given an IP address, enumerates and
counts the hostnames associated with it — the classic search-engine
`ip:`-style lookup that maps shared-hosting neighbours and forgotten vhosts
onto one address. Its purpose is attack-surface expansion during
reconnaissance: hostnames sharing the target IP often reveal additional
applications and legacy names in scope. The upstream repository is gone from
GitHub, no surviving mirror documents its command-line interface, and the
packaged code is Python-2-era, so no verified example commands can be given;
check `grabing -h` on an installed copy before relying on it. For the same
job against today's indexes, tools such as bing-ip2hosts (Bing `ip:` search)
and hakrevdns (bulk PTR sweeps) cover this role on live systems.

*No corpus evidence — no documented CLI survives upstream (repository
removed from GitHub); description derived from BlackArch package metadata.*
