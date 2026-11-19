---
name: "kamerka"
categories:
  - blackarch-recon
version: "48.1f301e8"
homepage: "https://github.com/s7x/kamerka"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-07"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "s7x/kamerka"
github-stars: 0
github-pushed: "2026-03-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# kamerka

> Builds an interactive map of internet-exposed cameras, printers, tweets and ICS devices around coordinates or across a whole country, by querying Shodan (and Flickr/Twitter) from one command.

## What it's for

Geospatial exposure hunting: give Kamerka a point and radius (or a country code) and it asks Shodan which cameras, printers, RTSP streams, MQTT brokers and industrial-control devices are exposed nearby, then renders every hit on a folium map with icons and popups (including "check ownership" and "lookup on Shodan" links per host). Additional modules pull geo-tagged tweets and Flickr photos for the same area. It is a mapping/query layer, not an exploit tool — it needs a Shodan subscription (API key configured in the script) plus the Twitter/Flickr/Google Street View keys for those modules. Note lineage: this is the community "fixed fork" (s7x) of woj-ciech's original Kamerka 2.0 (FIST) that BlackArch packages; the author's successor is the separate Kamerka-GUI project, and this CLI version is no longer developed.

## Typical use cases

### Map exposed devices around a location

```
python kamerka.py --lat 40.7128 --lon -74.0060 --radius 5 --camera --printer --dark
```

Outcome: queries Shodan within 5 km of the coordinates for cameras and printers (add `--rtsp`, `--mqtt`, `--twitter`, `--flickr` for the other modules) and writes an interactive HTML map — open it in a browser and click popups for host details; `--open` filters to reachable devices only. `--radius` defaults to 3 km; `--first`/`--last` page through Shodan results.

### Map a country's industrial control systems

```
python kamerka.py --country DE
```

Outcome: switches to whole-country mode and plots ICS/SCADA exposure for the given short country code (e.g. `US`, `DE`, `PL`) — the classic "critical-infrastructure on the internet" overview, one map per run.

### Deep-scan each found host and index results

```
python kamerka.py --country DE --rtsp --mqtt --recursive --elasticsearch --host 127.0.0.1 --port 9200
```

Outcome: `--recursive` makes Kamerka re-check every discovered host for open ports and richer per-host information (slower); with `--elasticsearch` the results are also shipped to the Elasticsearch node at `--host:--port` so runs accumulate into a searchable exposure database.

*No corpus evidence — derived from upstream documentation (README of the s7x fork packaged by BlackArch).*
