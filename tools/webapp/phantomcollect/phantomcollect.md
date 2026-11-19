---
name: "phantomcollect"
categories:
  - blackarch-webapp
version: "1.0.0"
homepage: "https://github.com/xsser01/phantomcollect"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "xsser01/phantomcollect"
github-stars: 20
github-pushed: "2025-11-21"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 34
gh-activity-checked: "2026-11-09"
---

# phantomcollect

> Serves a page that quietly collects visitor data — IP, geolocation, device fingerprint — into a local database.

## What it's for

PhantomCollect is a small Flask-based framework demonstrating browser-side data collection: it serves a page that, on visit, gathers the visitor's public IP with IP-geolocation, GPS position (if permitted), device and screen fingerprint, network connection info, and battery status, storing each visit in a local SQLite database with a real-time terminal display. It includes timezone-forensics logic that compares system and browser timezone data to flag location spoofing. Intended for authorized security awareness and education; all data stays local to the host running it.

## Typical use cases

### Start the collection interface

```
phantomcollect
```

Outcome: the web interface is served at `http://localhost:8080`; visits to the page are captured and appended to `victims.db`.

### Run on a different port

```
phantomcollect --port 8081
```

Outcome: the interface and collection endpoint move to port 8081 — for example to sit behind a reverse proxy or tunnel that exposes it to the test audience.

### Review captured visits

```
sqlite3 victims.db "SELECT * FROM victims;"
```

Outcome: prints every collected record — timestamp, IP, user agent, location, device info, and the full JSON payload — for analysis; deleting `victims.db` clears the dataset.

No corpus evidence — derived from upstream documentation.
