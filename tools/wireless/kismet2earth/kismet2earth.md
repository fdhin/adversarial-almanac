---
name: "kismet2earth"
categories:
  - blackarch-wireless
version: "2.0"
homepage: "https://code.google.com/archive/p/kismet2earth"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/kismet2earth"
github-stars: 0
github-pushed: "2025-12-17"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# kismet2earth

> Imports Kismet .netxml logs into SQLite and exports filtered Google Earth KML.

## What it's for

kismet2earth converts Kismet .netxml wardriving logs into KML through
an intermediate SQLite database: k2db parses the netxml detection-run
into a database, and db2ge renders that database to KML with filters
for WPA-only or open-only exports. The original Google Code tool was
written for Python 2; the packaged edition is a Python 3 modernization
of the same tooling. It targets classic Kismet netxml logs — modern
Kismet writes kismetdb and exports via kismetdb_to_kml or its web UI
([[kismet]]); see also [[kismet-earth]] for the older one-shot
converters.

## Typical use cases

### Import netxml into a database

Stage a wardrive log for filtering and reuse.

```
k2db -i wardrive.netxml -o wardrive.db
```

Outcome: parses the netxml and stores the networks with their GPS and
encryption attributes into the SQLite database `wardrive.db`, which you
can re-query with sqlite3 without re-parsing the XML.

### Export all networks to KML

Render the database for Google Earth.

```
db2ge -i wardrive.db -o wardrive.kml -a
```

Outcome: writes a Google Earth KML containing every network in the
database with its averaged GPS position.

### Export filtered sets

Produce a KML restricted to a security class.

```
db2ge -i wardrive.db -o wpa_only.kml -w
```

Outcome: a KML containing only WPA-encrypted networks; use `-n`
instead for open networks only.

*No corpus evidence — derived from upstream documentation.*
