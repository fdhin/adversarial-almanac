---
name: "bluelog"
categories:
  - blackarch-bluetooth
version: "1.1.2"
homepage: "http://www.digifail.com/software/bluelog.shtml"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2015-02-15"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "MS3FGX/Bluelog"
github-stars: 138
github-pushed: "2019-07-16"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bluelog

> Lean single-purpose Bluetooth scanner that logs every discoverable device it sees — designed to run unattended for hours at a fixed site.

*No corpus evidence — derived from upstream documentation.*

## What it's for

bluelog (Tom Nardi / MS3FGX) answers one survey question: how many discoverable Bluetooth devices pass through a location over time. It deliberately has no interactive interface — start it, walk away, read the log. The log format is fully configurable: device names, manufacturer (OUI) lookups, raw or human-readable device class, timestamps, and two privacy modes that either mask the device-specific half of each MAC or replace addresses entirely with CRC32-derived IDs. It only needs bluez, runs on x86, MIPS and ARM (it shipped on OpenWRT, Kali and Pwnie Express gear), and daemonizes cleanly. Its companion mode, Bluelog Live, renders the ongoing scan as auto-updating HTML pages for public display — bluelog generates the pages, your own web server serves them.

## Typical use cases

### Log a site survey to a file

```
sudo bluelog -i hci0 -o site-survey.log -v
```

Outcome: scans continuously on `hci0`, appending each newly discovered device (address, optionally name/class/manufacturer/timestamp) to `site-survey.log`; without `-o` the log is named `bluelog-YYYY-MM-DD-HHMM.log` in the current directory. Verbose mode mirrors finds on the console so you can sanity-check coverage, then Ctrl+C ends the scan with a clean shutdown line.

### Run unattended as a daemon

```
sudo bluelog -d -m -f
```

Outcome: forks to the background after startup and keeps logging until stopped with `bluelog -k`, with `-m` resolving the hardware manufacturer from each MAC's OUI and `-f` writing the device class in human-readable form (e.g. `0x7a020c` becomes `Smart Phone,(Net Capture Obex Audio Phone)`). This is the shape to use on a headless sensor; for privacy-sensitive surveys `-x` masks the device half of each MAC and `-e` logs only CRC32 device IDs.

### Show results live on a web page

```
sudo bluelog -l
```

Outcome: switches to Live mode — instead of (only) a plain log file, bluelog generates a self-updating set of HTML/CGI files suitable for a public "wall of devices" display, refreshed as devices are found. Serve the generated directory with any web server (Apache, lighttpd); bluelog itself is not a web server and stays unaware of whether anyone is watching.
