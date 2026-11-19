---
name: "revipd"
categories:
  - blackarch-recon
version: "5.2aaacfb"
homepage: "https://github.com/PypeRanger/revipd"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "PypeRanger/revipd"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# revipd

> Legacy Python 2 reverse-IP domain scanner; upstream is gone and no verifiable CLI documentation survives.

## What it's for

revipd is a simple reverse IP domain scanner — given an IP address or domain, it looked up the other domains resolving to the same host. Only the distro packaging survives as a record of what it was: a Python 2 script (packaged dependencies are `python2` and `python2-requests`) last built from the upstream git repository in 2016. The GitHub repository has been deleted and was never captured by an archive snapshot, so no README, usage text, or flag list can be verified — no example commands are given here for that reason. Despite the similar job, it is unrelated to `reverseip` (a Ruby tool by a different author) and to `ripdc` (a bash script by nullsecurity); both of those have verifiable, currently documented CLIs and are the practical choices today. A 2016-era Python 2 tool whose dependencies and data sources have both rotted is a museum piece, not a working option.

*No corpus evidence — no upstream documentation survives to derive from (repository deleted before archival; identity per the BlackArch recipe).*
