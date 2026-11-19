---
name: "kismet-earth"
categories:
  - blackarch-wireless
version: "0.1"
homepage: "https://www.blackarch.org/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# kismet-earth

> Converts classic Kismet .xml/.gps logs into Google Earth KML.

## What it's for

Two small 2007-era converters that turn wardriving logs from classic
Kismet (the .xml network dumps and .gps GPS tracks of that generation)
into KML for Google Earth. kismetearth.pl builds a wardriving map —
best position per BSSID picked by signal strength, bad coordinates and
duplicates dropped — or a full GPS trace for a single BSSID;
pykismetearth.py produces a KML folder per SSID with signal-strength
metadata and a strongest-signal location estimate. Neither reads
modern Kismet's kismetdb logs — for current Kismet use kismetdb_to_kml
or the web UI export ([[kismet]]); [[kismet2earth]] is a similar
classic-log converter with SQLite filtering.

## Typical use cases

### Build a wardriving map

Convert a directory of classic Kismet logs into one KML.

```
/usr/share/kismet-earth/kismetearth.pl /var/log/kismet
```

Outcome: reads every .xml/.gps pair in the directory and writes
`klog2kml.kml` (override the name with `-f`) with one pin per BSSID at
its strongest-signal coordinates — open the file in Google Earth to
review the survey.

### Map all GPS points for one access point

Visualize everywhere a single network was heard.

```
/usr/share/kismet-earth/kismetearth.pl -e -b 00:11:22:33:44:55 /var/log/kismet
```

Outcome: a KML with every GPS fix recorded for that BSSID, points
elevated by relative signal strength (`-e`), showing where the access
point was audible from — useful when triangulating a physical location.

### Convert one log with the Python variant

Use the per-log converter instead of the directory scanner.

```
/usr/share/kismet-earth/pykismetearth.py Kismet-Sep-07-2006
```

Outcome: parses the `Kismet-Sep-07-2006.xml`/`.gps` pair and writes a
KML with a folder per SSID containing signal metadata and a best-guess
location marker (Python 2 era script, packaged with a python2
shebang).

*No corpus evidence — derived from upstream documentation.*
