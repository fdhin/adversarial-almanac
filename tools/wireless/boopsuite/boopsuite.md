---
name: "boopsuite"
categories:
  - blackarch-wireless
version: "170.16c902f"
homepage: "https://github.com/M1ND-B3ND3R/BoopSuite"
packaged-by: [blackarch]
blackarch-builddate: "2019-01-27"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "M1ND-B3ND3R/BoopSuite"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# boopsuite

> Pure-Python Wi-Fi auditing suite by Jarad Dingman (M1ND-B3ND3R): monitor-mode manager, channel sniffer with handshake capture, and a deauthentication tool — no aircrack-ng dependency.

## What it's for

BoopSuite is a set of Python 3 tools covering the standard Wi-Fi audit loop without depending on the aircrack-ng suite: BoopMon toggles a card between managed and monitor mode (with renaming, channel selection and killing of interfering processes), BoopSniff shows access points and clients on 2.4 and 5 GHz and captures WPA handshakes to pcaps under ~/pcaps, and BoopStrike sends deauthentication floods, optionally targeting or sparing specific APs. A sniffer GUI (boopsniff_gui) also ships. The upstream GitHub repository has been deleted; the commands and flags below are from the final released interface (v3.0.1, 2018) — earlier releases spelled the commands lowercase (`boop`, `boopsniff`, `boopstrike`).

## Typical use cases

### Prepare a card for monitoring

```
BoopMon -i wlan0 -n boop0 -c 11 -k
```

Outcome: renames the card to boop0, switches it to monitor mode, locks it to channel 11 and kills interfering processes (NetworkManager and friends); `BoopMon -i wlan0mon` switches the card back to managed mode — BoopMon always toggles between the two.

### Sniff networks and clients, capturing handshakes

```
BoopSniff -i boop0 -c 6
```

Outcome: live view of access points and associated clients on channel 6; add `-u` to also show unassociated clients and `-f 5` (or `-f all`) to work the 5 GHz band. Whenever a client completes a WPA handshake, the capture file is written under ~/pcaps for offline cracking.

### Focus the sniffer on one network

```
BoopSniff -i boop0 -t 00:11:22:33:44:55
```

Outcome: restricts the display and capture to the target network — cleaner output while waiting on a single handshake; `-T` stops the tool after a set time.

### Force clients to re-associate (deauth)

```
BoopStrike -i boop0 -t 00:11:22:33:44:55 -p 5
```

Outcome: floods the target AP's clients with deauthentication frames (`-p` sets the packet count — more than 5 is usually unnecessary); `-s 00:11:22:33:44:55` spares one AP from the flood and `-f 5` hits the 5 GHz band. Pair with BoopSniff to capture the handshake as clients reconnect.

*No corpus evidence — derived from upstream documentation.*
