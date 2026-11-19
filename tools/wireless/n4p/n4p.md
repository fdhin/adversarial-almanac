---
name: "n4p"
gentoo-category: "net-wireless"
version: "2.2"
homepage: "https://github.com/Cyb3r-Assassin"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
github-repo: "Cyb3r-Assassin/n4p"
github-stars: 61
github-pushed: "2017-10-09"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# n4p

> Menu-driven attack framework that orchestrates rogue-AP, MITM, and WPA-cracking stacks from one console.

*No corpus evidence — derived from upstream documentation.*

## What it's for

N4P (by Cyb3r-Assassin, "for Pentoo and Gentoo Linux") wraps aircrack-ng, hostapd (including WPE and karma builds), sslstrip, ettercap, dhcpd, and iptables behind a numbered menu: reconnaissance scans, fake access points with automatic bridging and IPv4 forwarding, ARP spoofing against wired networks, WPA/WPA2 handshake capture and cracking via aircrack-ng or hashcat, and client/monitor windows. Attack parameters live in /etc/n4p/n4p.conf — interfaces, AP options, ettercap flags, ARP victims — which you re-edit between attacks, and captures, logs, and recon data accumulate under /tmp/n4p/. It is a 2017-era orchestrator with no new techniques of its own, and some bridging features assume Gentoo's OpenRC.

## Typical use cases

### Launch the attack menu

Start the interactive framework.

```
sudo n4p
```

Outcome: opens the numbered menu (recon, config editor, WPA2 handshake attack, cracking, monitors, firewall build, arpspoof, sslstrip); each choice spawns the underlying tools in terminal windows, and all captures and logs land under /tmp/n4p/ for later analysis. Review /etc/n4p/n4p.conf before attacking — interfaces and attack methods are configured there, not on the command line.
