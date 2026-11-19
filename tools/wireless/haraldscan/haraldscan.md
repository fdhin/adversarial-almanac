---
name: "haraldscan"
gentoo-category: "net-wireless"
version: "0.41"
homepage: "http://code.google.com/p/haraldscan/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: python
license: GPL-3
meta-checked: "2026-11-09"
github-repo: "xuxumon/haraldscan"
github-stars: 0
github-pushed: "2016-04-20"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# haraldscan

> Bluetooth discovery scanner that resolves every discovered device's MAC address to its vendor using a bundled OUI database.

## What it's for

Harald Scan (by Terence Stenvold, 2010-era Python) sweeps for nearby Bluetooth devices and, for each one, looks up the vendor from the first six MAC address characters (the OUI) in a local SQLite database built from the shipped MACLIST data. Its explicit goal is accumulating MAC-to-vendor mappings: devices whose vendor cannot be resolved get all collected information written to a file for submission back to the project. It can optionally run a service scan on discovered devices to enumerate what they expose, keep the database in memory for speed, and write discovery results to timestamped files. It is a discovery and inventory tool — it finds and fingerprints Bluetooth neighbors, it does not exploit them.

## Typical use cases

### Build the MAC vendor database (first run)

```
haraldscan -b
```

Outcome: constructs the local OUI database from the shipped MACLIST data and exits; run this once before the first scan, and re-run `haraldscan -u` periodically to pull updated MAC assignments (requires Internet).

### Scan for nearby Bluetooth devices

```
sudo haraldscan
```

Outcome: continuously discovers Bluetooth devices in range and prints each MAC with its resolved vendor; a devices-per-interval counter is shown every 15 minutes by default (`-t 30` changes the interval). Devices with unknown vendors are also dumped to files named after the first six MAC characters.

### Scan and enumerate services on each device

```
sudo haraldscan -s
```

Outcome: in addition to discovery, runs a service scan on every device found and saves the results to a per-device file, giving a picture of which profiles and services each neighbor exposes.

### Fast in-memory scan with output to a file

```
sudo haraldscan -m -w found.txt
```

Outcome: holds the MAC database in memory instead of on disk (faster) and writes all discovered device info to `found.txt` rather than an auto-generated timestamped filename; `--no-write` disables file output entirely and `--no-service` suppresses the automatic service scans on unknown devices.

*No corpus evidence — derived from upstream documentation (options verified against the packaged source's usage text).*
