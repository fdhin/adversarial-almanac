---
name: "bluepot"
categories:
  - blackarch-bluetooth
version: "0.2"
homepage: "https://github.com/andrewmichaelsmith/bluepot"
packaged-by: [blackarch]
blackarch-builddate: "2021-06-15"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "andrewmichaelsmith/bluepot"
github-stars: 279
github-pushed: "2026-04-02"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# bluepot

> Bluetooth honeypot with a desktop-style GUI: accepts incoming attacks and malware over Bluetooth and records who sent what.

*No corpus evidence — derived from upstream documentation.*

## What it's for

bluepot (a third-year university project by Andrew Michael Smith) is a Linux Bluetooth honeypot written in Java. Attached dongles make it look like an attractive, vulnerable Bluetooth device; it accepts and stores any malware pushed to it over OBEX and interacts with the classic attack tools — bluebugging and bluesnarfing attempts — instead of just ignoring them, so attackers keep coming. Everything is observed through a graphical dashboard: live attack lists, graphs, and drill-down analysis from the underlying log files. It needs root, at least one Bluetooth receiver (more dongles = more concurrent attack surface) and the bluez development libraries. It is a monitoring/teaching instrument, not a hardening tool — the point is to capture and study attacks, so run it on hardware you are content to expose.

## Typical use cases

### Start the honeypot with its monitoring dashboard

```
sudo java -jar bluepot.jar
```

Outcome: opens the bluepot GUI, puts every attached adapter into honeypot mode, and begins logging: incoming connections and attack attempts appear in the dashboard lists, graphs track activity over time, and any file received over OBEX is stored for later inspection. Leave it running at a fixed location (conference, office entrance) to sample what attacks the radio spectrum actually attracts.
