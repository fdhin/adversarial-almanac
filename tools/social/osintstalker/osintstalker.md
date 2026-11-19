---
name: "osintstalker"
categories:
  - social
version: "2014.1.4"
homepage: "https://github.com/milo2012/osintstalker/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "milo2012/osintstalker"
github-stars: 730
github-pushed: "2015-04-03"
github-archived: false
github-issues: 29
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# osintstalker

> fbStalker - OSINT tool for Facebook - Based on Facebook Graph and other stuff

## What it's for

OSINT Stalker is a pair of Python 2 scripts from 2013–2014 accompanying the
"HackInTheBox: Facebook OSINT" research. fbStalker drives a logged-in
Chrome session through Facebook and the Graph API to map a target's
timeline, friends, likes, places and photos into a searchable database and
graph. geoStalker pivots on a location: given an address or GPS
coordinates it queries geotagged sources (Flickr, Instagram, Twitter,
Wigle), then sweeps the user IDs it finds across Facebook, YouTube,
Instagram, LinkedIn and Google to link people to a place. Both scripts are
Python 2 legacy code and depend on long-dead library versions and
APIs — treat them as historical reference implementations of the
techniques rather than turnkey tools. Facebook credentials (for fbStalker)
and a set of API keys/tokens (for geoStalker) must be filled into the
scripts' source before use.

## Typical use cases

### Map a Facebook target's activity

Profile one Facebook account through an authenticated browser session.

```
python2 fbstalker1.py -user target
```

Outcome: After the credentials embedded in the script log in, crawls the
target's public and friend-visible activity into a local database and
writes a report; without `-report` a default report file is produced.

### Map a Facebook target into a named report file

Keep the output of a run for later analysis.

```
python2 fbstalker1.py -user target -report target_report
```

Outcome: Same crawl as above, but the generated report and graph artifacts
are written under the given report name.

### Find social accounts tied to a location

Pivot from a place to the people who post there.

```
python2 geostalker.py -location "1.358143,103.944826"
```

Outcome: Queries the geotagged sources configured in the script for the
coordinates, then checks every discovered user ID across the supported
social networks and prints the cross-network account links. Without
`-location` the script prompts for an address or GPS coordinates
interactively.

*No corpus evidence — derived from upstream documentation.*
