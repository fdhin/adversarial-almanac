---
name: "fuzzap"
categories:
  - blackarch-wireless
version: "17.057002b"
homepage: "https://github.com/lostincynicism/FuzzAP"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "lostincynicism/FuzzAP"
github-stars: 80
github-pushed: "2016-11-14"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fuzzap

> Obfuscate the wireless landscape by beacon-flooding fake access points via packet injection.

## What it's for

FuzzAP (lostincynicism) is a Python 2/scapy script for obfuscating wireless networks: it injects beacon frames that make dozens or hundreds of fictitious access points appear in Wi-Fi scanners and site-survey tools. Unlike the older FakeAP and AirRaid tools it cites, it never actually configures a real AP — it only injects frames that look like they came from one — so any card whose drivers support monitor mode and injection works. It combines a vendor-OUI list with an SSID wordlist (the bundled `common.txt` and `ssid.txt`) so the fake networks look plausibly like real consumer gear. Clients cannot associate to these phantom networks; the purpose is to confuse scanning and survey tooling (or to bury a real network in noise).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Flood a monitor interface with fake access points

Generate a wave of plausible-looking phantom networks to test how scanner and survey tools cope.

```
sudo fuzzap wlan0mon 50 common.txt ssid.txt
```

Outcome: after putting the card in monitor mode, injects beacon frames (and probe responses) for 50 fake APs built from random OUIs and SSIDs in the two wordlist files; nearby scanners list the fictitious networks. The interface must already be a monitor interface (e.g. created with airmon-ng), and the terminal shows a per-packet "Sent 1 packets" line in this early version.
