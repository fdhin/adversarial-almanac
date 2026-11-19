---
name: "wpaxfuzz"
categories:
  - fuzzer
version: "git (2026-06-15)"
homepage: "https://github.com/efchatz/wpaxfuzz"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "efchatz/wpaxfuzz"
github-stars: 216
github-pushed: "2026-06-15"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
worth: 5.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 4
---

# wpaxfuzz

> Research-grade 802.11 fuzzer for management, control and data frames and the WPA3-SAE handshake.

## What it's for

WPAxFuzz (Kampourakis, Chatzoglou et al.) transmits mutated 802.11 frames at an access point and its associated client from a monitor-mode interface, watching whether the client's connection survives. It covers management, control and (in beta) data frames with either standard-conforming or randomly sized fields seeded by the blab generator, the WPA3-SAE commit/confirm exchange in standard and extensive modes, and a DoS replay module that retransmits exactly the frames the fuzzer recorded as harmful. The methodology has produced published CVEs against MediaTek and other chipsets. It is a menu-driven Python 3/Scapy research tool for Wi-Fi implementation testing, not a general-purpose network fuzzer, and it expects the target STA and the fuzzer on separate physical machines to avoid false aliveness readings.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Prepare the monitor interface

Put the wireless card into monitor mode and lock it to the target AP's channel.

```
sudo airmon-ng check kill
sudo airmon-ng start wlan0
sudo iw wlan0mon set channel 6 HT20
```

Outcome: the WNIC runs in monitor mode on the AP's channel with HT20 width — the prerequisite for both transmitting fuzzed frames and observing the association (find the channel first with `sudo airodump-ng wlan0mon`).

### Run a fuzzing campaign against an AP/STA pair

Launch the fuzzer with the target pair configured.

```
sudo python3 fuzz.py
```

Outcome: after filling in `src/config.json` (AP MAC, associated STA MAC, SSID, wireless interface), the menu offers the modules — 1 management frames, 2 SAE exchange, 3 control frames, 4 data frames (beta), 5 DoS replay. Choosing a module and a standard/random (SAE: standard/extensive) mode starts transmitting mutated frames; when a connection interruption or deauthentication is detected the fuzzer prompts to reconnect the STA and resume, logging every frame and disruption for the DoS module to replay later. Two consecutive Ctrl+C exit the run.
