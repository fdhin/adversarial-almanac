---
name: "airflood"
categories:
  - blackarch-wireless
version: "0.1"
homepage: "http://packetstormsecurity.com/files/51127/airflood.1.tar.gz.html"
packaged-by: [blackarch]
blackarch-builddate: "2019-01-25"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/airflood"
github-stars: 0
github-pushed: "2019-09-06"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# airflood

> 802.11 denial-of-service tool that fills an access point's association table with fake clients until no real station can connect.

## What it's for

airflood performs a single, narrowly-scoped attack known as "access point overloaded": it performs fake associations against a target AP using randomly spoofed MAC addresses until the AP's client table is exhausted, then keeps the fake entries alive with keep-alive traffic, so legitimate stations are refused service. It is a 2006 modification of aireplay-ng (from the [[aircrack-ng]] family) with a Spanish-language interface and Spanish-flag output messages; the packaged upstream is the original 0.1 tarball by Nilp0inteR. It requires root and a monitor-mode interface, takes the ESSID and BSSID of the target as arguments, and cannot sniff, capture handshakes, or crack anything — it is purely a flooding/DoS tool, and many modern APs are no longer vulnerable to table exhaustion.

## Typical use cases

### Flood an AP's client table once

Fill the target's association table with fake clients in a single pass.

```
sudo airflood -F -e OfficeNet -a 00:11:22:33:44:55 wlan0mon
```

Outcome: associates spoofed random MACs with the AP (BSSID `-a`, ESSID `-e`) over the monitor interface `wlan0mon` until its client table is full, printing keep-alive progress (in Spanish, e.g. "Enviando keep-alive"); genuine stations are subsequently refused association. Running with a missing or incomplete argument set prints the usage block and exits.

### Flood continuously with keep-alive refresh

Keep the fake association table pinned indefinitely.

```
sudo airflood -L -e OfficeNet -a 00:11:22:33:44:55 -d 5 wlan0mon
```

Outcome: `-L` runs the same flood in an endless loop, re-sending keep-alive packets every `-d` seconds so entries never time out; the attack continues until interrupted, keeping the AP's client table permanently occupied.

### Expel a specific client while flooding

Displace one legitimate station and block new associations at the same time.

```
sudo airflood -F -e OfficeNet -a 00:11:22:33:44:55 -h AA:BB:CC:DD:EE:FF -c 30 wlan0mon
```

Outcome: in addition to the flood, the genuine client given by `-h` is targeted with bursts of `-c` deauthentication packets per send, forcing it off the network while fake clients occupy the table. `-r` tunes authentication retries (default 3) if the AP rejects the fake associations.

*No corpus evidence — derived from upstream documentation.*
