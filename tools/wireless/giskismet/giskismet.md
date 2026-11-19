---
name: "giskismet"
categories:
  - blackarch-wireless
version: "2.9ebe9d0"
homepage: "https://www.giskismet.org"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-14"
build-system: perl
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "xtr4nge/giskismet"
github-stars: 58
github-pushed: "2016-08-26"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# giskismet

> Imports Kismet .netxml survey output into SQLite and queries it back out as Google Earth KML layers.

## What it's for

GISKismet (by Joshua D. Abraham) is a wireless-recon visualization front end for Kismet: it parses the .netxml files produced by Kismet-newcore sweeps (or the CSV logs of older Kismet-devel), stores every access point and client in an SQLite database, and then generates KML files you can open in Google Earth from arbitrary SQL queries. Import-time filters (BSSID, ESSID, channel, encryption) and query-time SQL give flexible slicing of a wardrive dataset. It does no capturing itself — it is purely post-processing for data another tool already gathered.

## Typical use cases

### Import a Kismet-newcore sweep into the database

```
giskismet -x Kismet-Feb-05-2009-1.netxml
```

Outcome: every AP and client record in the .netxml is inserted into the SQLite database `wireless.dbl` (default name); repeat with further .netxml files to accumulate a survey. Use `--database survey.dbl` to keep separate databases per engagement.

### Import only a slice of a sweep

```
giskismet -x Kismet-Feb-05-2009-1.netxml --channel 2,6,11
```

Outcome: only records matching the filter (here channels 2, 6 and 11) are inserted; `--bssid`, `--essid` and `--encryption` accept the same file-or-comma-separated-list form. Add `--ignore-gps` when the capture card had no GPS fix, and `-s` to silence the per-AP progress output.

### Plot the whole database on Google Earth

```
giskismet -q "select * from wireless" -o all-aps.kml
```

Outcome: writes `all-aps.kml` with one placemark per database row, positioned at the recorded GPS coordinates; open it in Google Earth to see the coverage footprint of the surveyed networks.

### Plot a filtered layer with a name

```
giskismet -q "select * from wireless where ESSID='linksys' and Encryption='None'" -o open-linksys.kml -n OpenLinksys
```

Outcome: writes a KML layer containing only open networks named linksys; `-n` sets the layer name shown in Google Earth and `--desc` its description, so multiple filtered layers from the same survey stay distinguishable.

### Dump an arbitrary query as CSV

```
giskismet -m "select BSSID,ESSID,Channel,Encryption from wireless" > survey.csv
```

Outcome: prints the query result as CSV to stdout instead of KML, ready for spreadsheets or further scripting; combined with SQL aggregates this doubles as a quick report generator over the survey data.

*No corpus evidence — derived from upstream documentation (usage and examples verified against the packaged source's help, man page and EXAMPLES.txt).*
