---
name: "pi-pwnbox-rogueap"
categories:
  - wireless
version: "git (2026-06-08)"
homepage: "https://github.com/koutto/pi-pwnbox-rogueap"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "koutto/pi-pwnbox-rogueap"
github-stars: 2115
github-pushed: "2026-06-08"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 5
---

# pi-pwnbox-rogueap

> Deployment project that turns a Raspberry Pi plus Alfa USB WiFi adapters into a headless, battery-powered WiFi attack box with remote access.

## What it's for

pi-pwnbox-rogueap is not a single program but a reproducible build-out: scripts and configuration files that install Kali Linux ARM on a Raspberry Pi 3/4/5 with a fixed three-NIC layout — the Pi's built-in WiFi for internet uplink, an AP-capable USB adapter (e.g. RTL8812AU) that automatically spawns a hidden administration network at boot, and a monitor-mode/injection-capable adapter (Alfa AWUS036NEH/ACH) for the actual WiFi attacks. Remote access is by SSH and by Apache Guacamole (VNC-over-HTTP on port 8080), so GUI tools such as airgeddon can be driven from a phone or laptop with no screen attached. The repository also ships WiFi-hacking cheatsheets and a mind map (in the wiki and `mindmap/`). It contains no attack code of its own — its value is the curated rig: persistent interface names, auto-started services, and a known-good hardware pairing. Building the box (flashing, `scripts/install-system.sh`, Guacamole setup) is documented in `install.md`.

## Typical use cases

### Reach the box over its administration network

Connect to the AP it auto-spawns at boot and log in remotely.

```
ssh kali@10.0.0.1
```

Outcome: after joining the hidden administration SSID, the PwnBox is always reachable at its fixed AP address 10.0.0.1 — SSH on port 22 for CLI tools, and `http://10.0.0.1:8080/guacamole` for the VNC-over-HTTP session used by GUI tools; on a LAN instead of the admin network, the address comes from DHCP (find it with netdiscover).

### Join a new uplink network from the box

Add WiFi credentials so the built-in chip reconnects on later boots.

```
wpa_passphrase <SSID> <passphrase> >> /etc/wpa_supplicant.conf
wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant.conf
dhclient -v wlan0
```

Outcome: appends the network to the persistent supplicant config and brings the uplink up immediately; the Pi reconnects to this network automatically at boot whenever it is in range.

### Free memory by stopping the Guacamole stack

Shut down the heaviest service when the box is used headless from SSH only.

```
./stop-guacamole.sh
```

Outcome: stops the Guacamole/Tomcat/MySQL chain that can otherwise pin a large share of the Pi's RAM; `start-guacamole.sh` brings the web VNC gateway back (it auto-detects Tomcat 9 vs 10).

### Switch the DHCP backend for tools that need isc-dhcp-server

Some attack frameworks want the full ISC server instead of dnsmasq.

```
systemctl stop dnsmasq
systemctl start isc-dhcp-server
```

Outcome: releases the port 67/udp conflict — dnsmasq normally serves the administration AP, and isc-dhcp-server is shipped disabled by default precisely to avoid this clash, so the swap is only made while a tool that requires it is running.

*No corpus evidence — derived from upstream documentation.*
