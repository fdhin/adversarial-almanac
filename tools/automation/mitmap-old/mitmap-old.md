---
name: "mitmap-old"
categories:
  - blackarch-automation
version: "0.1"
homepage: "http://www.darkoperator.com/tools-and-scripts/"
packaged-by: [blackarch]
blackarch-builddate: "2017-02-04"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# mitmap-old

> 2008-era bash script that stages a karma-style rogue access point with DHCP, NAT routing, and ettercap-assisted capture.

## What it's for

`mitmap-old` (by Carlos Perez, darkoperator.com) automates the classic rogue-AP man-in-the-middle setup in one command: it brings the wireless card into monitor mode, launches `airbase-ng` in karma mode (`-P -C 60`, responding to every probe request so clients auto-associate), writes a DHCP configuration and starts `dhcpd` for the fake network (10.0.0.0/24 by default), and wires `iptables` NAT so associated clients reach the internet through the chosen upstream interface; ettercap is used for packet capture and traffic manipulation. It targets aircrack-ng-supported injection cards and 2008-era drivers (madwifi/DigiNinja karma patches) — the two `apa`/`apaf` modes require those patched drivers and will not work on modern stacks, while the `ap`/`apf` airbase-ng modes only need aircrack-ng. Treat it as a legacy/teaching tool; modern replacements (hostapd-mana, wifiphisher, airgeddon) are easier to run on current hardware.

## Typical use cases

### Launch a karma fake access point

Bridge wireless clients onto your wired uplink.

```
mitmap-old -m ap -i wlan0 -o eth0 -s "Free Wifi"
```

Outcome: the script randomizes the card's MAC, starts monitor mode via airmon-ng, launches `airbase-ng -P -C 60 -e "Free Wifi"` as the karma AP, generates `/tmp/dhcpd.conf` (subnet 10.0.0.0/24, leases 10.0.0.100–254), starts dhcpd on the airbase interface, and flushes/creates iptables NAT rules routing clients out through `eth0`. Every action is appended to a timestamped `mitmap-<date>.log` under the log folder (`/root/` by default). Clients that probe for any SSID get answered and associate with you; Ctrl-C triggers the cleanup trap that kills the spawned processes.

### Restrict the fake AP to specific clients

Only allow a whitelist of MAC addresses to associate.

```
mitmap-old -m apf -i wlan0 -o eth0 -f allowed.macs -s "Corp WiFi"
```

Outcome: same karma AP, DHCP, and NAT chain as above, but `airbase-ng` is launched with `--clients allowed.macs` so only the one-MAC-per-line entries in the filter file can associate — useful in a lab to keep the rogue AP from swallowing every nearby device.

### Supply a custom DHCP configuration

Control the address pool and DNS handed to victims.

```
mitmap-old -m ap -i wlan0 -o eth0 -d custom-dhcpd.conf
```

Outcome: the script skips config generation and runs `dhcpd` against `custom-dhcpd.conf` on the airbase interface, so the range, router, and `option domain-name-servers` (e.g. pointing at your own resolver) are exactly what you wrote; the rest of the fake-AP chain is unchanged. `-h` prints the full usage block listing all modes and options.

*No corpus evidence — derived from the packaged upstream source.*
