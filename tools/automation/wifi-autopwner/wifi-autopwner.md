---
name: "wifi-autopwner"
categories:
  - blackarch-automation
version: "36.faa4d01"
homepage: "https://github.com/Mi-Al/WiFi-autopwner"
packaged-by: [blackarch]
blackarch-builddate: "2020-01-08"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Mi-Al/WiFi-autopwner"
github-stars: 297
github-pushed: "2019-01-14"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wifi-autopwner

> Menu-driven bash assistant that chains aircrack-ng, Reaver/PixieWPS, RouterSploit and Nmap to audit every Wi-Fi network in range.

## What it's for

WiFi-autopwner (by Alexey Miloserdov / Mi-Al) is a single bash script that wraps the standard Wi-Fi attack toolchain behind one numbered text menu: it puts your adapter into monitor mode (and can raise TX power), then runs WPS Pixie Dust and known-PIN attacks via Reaver/PixieWPS, captures WPA/WPA2 handshakes for every AP in range and brute-forces them, performs classic WEP attacks, queries the 3WiFi database for known AP passwords, bypasses captive portals, and follows a successful connection with Nmap scanning plus a RouterSploit check of the router. Cracked passwords are collected centrally in `all_wifi_passwords.txt`. It is an orchestration layer, not a new attack: each menu item shells out to aircrack-ng, reaver, pixiewps, wireshark, nmap, routersploit, hashcat, crunch and friends, which must be installed separately. Development stopped around 2019-2020 and the menu texts mix English and Russian, but every underlying technique still applies to networks with WPS enabled or weak PSKs. The one-shot `71` automated-audit entry runs the whole sequence unattended.

## Typical use cases

### Launch the attack menu and start an audit

```
cd WiFi-autopwner/
sudo bash wifi-autopwner.sh
```

Outcome: opens the numbered menu as root. Pick `11` to select your wireless interface and `12`/`13` to set monitor mode, then choose an action — e.g. `42` Pixie Dust against all WPS-enabled APs, `52` capture handshakes of every AP plus brute-force, or `71` for the fully automated audit of all nearby networks. Recovered passwords print on screen and accumulate in `all_wifi_passwords.txt`.

*No corpus evidence — derived from upstream documentation.*
