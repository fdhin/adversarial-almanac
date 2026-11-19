---
name: "braces"
categories:
  - blackarch-bluetooth
version: "0.5"
homepage: "http://braces.shmoo.com/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-12-10"
build-system: perl
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/braces"
github-stars: 0
github-pushed: "2025-12-10"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# braces

> Bluetooth wardriving tracker: a Perl sensor that logs discoverable devices and reports them to a PHP/MariaDB web application.

## What it's for

braces is a Bluetooth tracking utility originating from the Shmoo Group and modernized by its current maintainers: a Perl script (packaged as `braces`, classically `braces.pl`) performs periodic Bluetooth inquiries and POSTs each set of discovered devices to a "mothership" web application (nginx + PHP + MariaDB, database initialized from `init_db.sql`) that stores and presents sightings over time. Use it to build a running picture of which devices repeatedly appear at a location — the classic Bluetooth wardriving setup. It is a two-part system: the sensor only produces data once the web backend is up, the mothership URL is set inside the script, and each sensor is labeled with a human-readable location name that shows up in the web view. The modernized edition dropped the deprecated `hcitool` in favor of direct BlueZ D-Bus calls.

## Typical use cases

### Run a sensing station

Start continuous inquiry at a named location.

```
braces 60 "main lobby"
```

Outcome: performs a Bluetooth inquiry every 60 seconds and POSTs each result set to the configured mothership; a local `scan.txt` debug file records the exact POST payloads. The first argument is the seconds between inquiries (60 is a sensible default) and the second is the location label reported with every sighting.

### Review collected sightings in the web frontend

Check what the sensors have gathered.

```
curl http://localhost/index.php
```

Outcome: returns the tracker page served by the tool's nginx/PHP frontend, listing devices seen with their sighting locations and times from the MariaDB database — a 200 response here confirms the backend stack is correctly wired to the sensors.

*No corpus evidence — derived from upstream documentation.*
