---
name: "beholder"
categories:
  - blackarch-wireless
version: "0.8.10"
homepage: "http://www.beholderwireless.org/"
packaged-by: [blackarch]
blackarch-builddate: "2015-01-17"
build-system: make
license: AMS
meta-checked: "2026-11-09"
github-repo: "nmurilo/BeholderWifi"
github-stars: 2
github-pushed: "2023-08-20"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# beholder

> Beacon-driven wireless IDS that alerts on rogue access points, evil-twin ESSID tricks and karma-style attacks in a monitored Wi-Fi environment.

## What it's for

beholder is a small C wireless intrusion-detection sensor by Nelson Murilo: it watches 802.11 beacon frames on a Wi-Fi interface and raises alerts when the environment stops looking legitimate — ESSID, MAC, encryption or channel changes on known networks, new suspicious APs, lookalike ESSID names (evil twins), the sudden disappearance of expected APs, and some types of jamming. It recognises the behaviour of passive rogue-AP tools such as karma, airbase-ng and hotspotter. It does not put cards into monitor mode, does not sniff non-Wi-Fi interfaces, does not crack WEP/WPA and does not watch authentications — it is purely a beacon-watching alerter, so bring your own monitor-mode interface. The original beholderwireless.org site is long defunct; the source lives on in the author's GitHub repository (BeholderWifi) and in the pangeia.com.br release tarball that the BlackArch package builds.

## Typical use cases

### Baseline the legitimate networks, then watch

```
beholder -a wlan0
```

Outcome: scans on the given interface and prints console alerts when a new suspect network appears or a known one changes ESSID/MAC/encryption/channel; `-a` adds newly seen networks to the initial network table (the usual option on a first run against your own environment).

### Detect evil-twin ESSID variations with a regex

```
beholder -r ".*[1i]nf[0o]s[3e]c.*" wlan0
```

Outcome: alerts on any network whose ESSID case-insensitively matches leet-speak variants of the watched name ("0infosec", "inf0sec", "1nfosec-1", ...) — the classic karma/evil-twin detection. Without regexes, `-c` (clever mode) flags similar ESSIDs by length and name instead.

### Alert when expected APs disappear

```
beholder -r ".*[1i]nf[0o]s[3e]c.*" -m ".*companynet.*" wlan0
```

Outcome: combines the variation watch with a disappearance watch — networks matching the `-m` expression that vanish from the air raise an alert, pointing at jamming, power loss or an AP being swapped.

### Report to syslog for central monitoring

```
beholder -s wlan0
```

Outcome: alerts are sent to syslog instead of the console, so a log host with swatch can chain email/SMS/SNMP traps; `-d` adds debug output and `-dd` verbose logging.

*No corpus evidence — derived from upstream documentation.*
