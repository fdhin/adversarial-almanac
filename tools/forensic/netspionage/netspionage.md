---
name: "netspionage"
categories:
  - blackarch-forensic
version: "99.c24f995"
homepage: "https://github.com/ANG13T/netspionage"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ANG13T/netspionage"
github-stars: 306
github-pushed: "2024-11-29"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netspionage

> Menu-driven network analysis console for LAN scanning, WiFi surveys, MAC-address OSINT and attack detection.

## What it's for

netspionage (by Angelina Tsuboi) is an interactive Python console for network awareness on your own LAN: option 1 scans — connected devices with IP/MAC/OS, nearby WiFi access points, or open ports on a subnet; option 2 performs reconnaissance on MAC addresses (device lookups for a scanned host or for any MAC you enter); option 3 detects ARP-spoofing and TCP SYN-flood attacks on a given port. Settings live in `configuration.ini` beside the program (`WiFiInterface`, `LogToTextFile`, `Color`); WiFi scanning needs the adapter already in monitor mode (e.g. via airmon-ng, then set `WiFiInterface=wlan0mon`). There is no non-interactive mode — everything happens through the numbered menus, so it is a console to sit in rather than a script to call.

## Typical use cases

### Start the console and enumerate your LAN

Open the toolkit and map hosts, ports and MAC-address data from its menus.

```
sudo netspionage
```

Outcome: prints the main menu (1 Scanning, 2 Reconnaissance, 3 Detection, 4 Exit); option 1 scans for connected devices, nearby APs, or open ports (the host/port scans prompt for a target subnet such as `198.51.100.0/24`), and option 2 looks up vendor/device data for any MAC address you enter. Option 3 monitors the subnet for ARP spoofing, or a chosen port for TCP flooding.

### Survey WiFi access points in monitor mode

List nearby wireless networks with a monitor-mode adapter.

```
sudo -E netspionage
```

Outcome: started with the environment preserved, after putting the adapter in monitor mode and pointing `WiFiInterface=` at it (e.g. `wlan0mon`) in `configuration.ini`, menu option 1 then 2 lists nearby access points with dBm signal, SSID, channel and encryption.

*No corpus evidence — derived from upstream documentation.*
