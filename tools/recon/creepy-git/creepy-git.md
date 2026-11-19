---
name: "creepy-git"
categories:
  - recon
version: "r137.9f60449"
homepage: "http://github.com/ilektrojohn/creepy"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ilektrojohn/creepy"
github-stars: 1501
github-pushed: "2016-01-07"
github-archived: false
github-issues: 61
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# creepy-git

> Geolocation information gatherer that maps a target's coordinate trail from social-network posts.

## What it's for

Creepy (by Yiannis Kakavas) is a geolocation OSINT application: it harvests
geotagged content posted by a person across supported social networking
platforms (via per-service plugins), then presents every collected coordinate
on a map, supports filtering by exact location and date range, and exports the
result set as CSV or KML for further analysis in other tools. It is a Python/
Qt desktop application driven entirely through its GUI — there is no command-
line or scripting interface, so it cannot be scripted into a pipeline. Treat
it as an archived-era tool: upstream development stopped in 2016 and the
plugins age as the underlying platform APIs change or shut down. Validate
plugin behavior against current platform APIs before relying on results.

No corpus evidence and no verifiable CLI — description-only note.
