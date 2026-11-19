---
name: "find3"
categories:
  - blackarch-misc
version: "604.5964026"
homepage: "https://github.com/schollz/find3"
packaged-by: [blackarch]
blackarch-builddate: "2021-08-20"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "schollz/find3"
github-stars: 4812
github-pushed: "2022-12-30"
github-archived: false
github-issues: 78
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# find3

> Indoor positioning framework that tracks devices from WiFi and Bluetooth fingerprints.

## What it's for

FIND3 (Framework for Internal Navigation and Discovery) locates WiFi/Bluetooth-emitting devices inside a building by fingerprinting: scanner clients sample the radio environment, the machine-learning server learns the fingerprint of each location, and later observations are classified back to a location. The packaged `find3` binary is the project's entry point that serves its web interface on port 9999; the fingerprint-gathering clients (command-line scanner, Android app, ESP8266/ESP32 firmware) are separate repositories that submit observations to the framework's `/learn` and `/track` HTTP endpoints. It is a passive listening system, not an active scanner of remote networks.

## Typical use cases

### Serve the framework web interface

Start the bundled server locally.

```
find3
```

Outcome: the process logs `serving on 0.0.0.0:9999` and the framework's web interface becomes reachable at http://127.0.0.1:9999 for setting up learning and tracking; stop it with Ctrl-C.

*No corpus evidence — derived from upstream documentation.*
