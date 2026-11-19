---
name: "kismon"
categories:
  - blackarch-wireless
version: "1.0.3"
homepage: "https://www.salecker.org/software/kismon.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-13"
build-system: python
license: BSD
meta-checked: "2026-11-09"
github-repo: "Kismon/kismon"
github-stars: 137
github-pushed: "2021-03-14"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kismon

> GTK GUI for Kismet with a live map and signal graphs.

## What it's for

Kismon is a desktop client that visualizes Kismet output: a live
OpenStreetMap of discovered networks, per-network signal graphs, and
import/export of netxml (classic Kismet), CSV and its own JSON format,
including Google Earth KMZ export. It speaks Kismet's REST interface —
its compatibility list covers Kismet 2019-12-R2 through 2020-12-R3
(kismon 1.0.3), so check your Kismet version ([[kismet]]) before
relying on it; it predates current Kismet releases. It neither captures
nor cracks — it is a viewer that attaches to a running Kismet server
(see also the classic-log converters [[kismet2earth]] and
[[kismet-earth]]).

## Typical use cases

### Launch the GUI

Attach to a running Kismet server and watch networks appear.

```
kismon
```

Outcome: opens the GTK window and connects to the Kismet server
configured in `~/.kismon/kismon.conf`, plotting networks, clients and
signal traces on the live map as they are discovered; F11 toggles
fullscreen, Ctrl+i / Ctrl+o zoom the map.

### Start without the map

Skip the map pane when osm-gps-map is unavailable or unwanted.

```
kismon --disable-map
```

Outcome: kismon starts with the OpenStreetMap widget disabled, keeping
the network list and signal graphs.

*No corpus evidence — derived from upstream documentation.*
