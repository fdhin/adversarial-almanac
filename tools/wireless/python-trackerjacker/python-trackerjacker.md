---
name: "python-trackerjacker"
categories:
  - blackarch-wireless
version: "2.0.5"
homepage: "https://github.com/calebmadrigal/trackerjacker"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "calebmadrigal/trackerjacker"
github-stars: 2745
github-pushed: "2026-03-22"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
---

# python-trackerjacker

> "Nmap for WiFi you're not connected to" — maps nearby networks and their clients from raw 802.11 traffic, and fires triggers when watched devices hit a byte threshold.

## What it's for

trackerjacker passively monitors 802.11 frames on a monitor-mode interface and does two jobs. Map mode builds a YAML inventory (`wifi_map.yaml`) of every network in range with its BSSID, channels, signal strength, vendor, and per-device client lists including each client's byte counts — usable as input to your own scripts. Track mode watches specific MAC addresses (or whole APs) and, when a device crosses a byte threshold inside a time window, runs a trigger: an arbitrary shell command or a Python plugin, e.g. alerting when an IP camera suddenly uploads a burst (motion detected), or running the builtin `foxhunt` plugin that live-sorts all visible transmitters by signal strength to physically locate a device. It has no attack capability beyond observation — the roadmap's "jack" deauth mode was never shipped. Monitor mode and channel hopping are handled automatically (or forced with the utility flags), and everything can also be driven from a JSON config file.

## Typical use cases

### Map all nearby networks and their clients

Build the YAML database of who is talking to whom.

```
trackerjacker -i wlan0mon --map
```

Outcome: channel-hops and sniffs until stopped, writing `wifi_map.yaml` in the working directory — per AP: bssid, ssid, channels, vendor, signal, cumulative bytes, and a devices dict with per-client bytes/signal/vendor; because it is plain YAML it feeds directly into scripted analysis.

### Trigger an alert when a device transmits heavily

Watch one MAC and fire a command at a byte threshold.

```
trackerjacker --track -m 00:11:22:33:44:55 --threshold 400000 --trigger-command "./alert.sh" --channels-to-monitor 1,6,11
```

Outcome: monitors only the listed channels and prints `[@] Device (...) threshold hit: <bytes>` each time the watched MAC crosses 400000 bytes, invoking `./alert.sh` per hit — the classic use is detecting an IP camera's upload burst as a proxy for "motion just happened"; `--trigger-cooldown` throttles repeat fires.

### Locate a transmitter by signal strength

Run the builtin foxhunt plugin as a live power meter.

```
trackerjacker -i wlan0mon --track --trigger-plugin foxhunt
```

Outcome: renders a curses table of every visible device sorted by dBm with OUI-resolved vendor — walk the device around and the target's row climbs as you close in; writing your own plugin is the same interface (a `trigger(**kwargs)` function or `Trigger` class in a Python file passed to `--trigger-plugin`).

### Prep an interface for other tools

Set monitor mode or a fixed channel explicitly.

```
trackerjacker --monitor-mode-on -i wlan0
trackerjacker --set-channel 11 -i wlan0
```

Outcome: enables monitor mode (or switches it off again with `--monitor-mode-off`) and pins a channel without touching airmon-ng — trackerjacker does both automatically during its own runs, so these matter when preparing the interface for another application; a full session can also be launched from a config file with `trackerjacker -c my_config.json`.

*No corpus evidence — derived from upstream documentation.*
