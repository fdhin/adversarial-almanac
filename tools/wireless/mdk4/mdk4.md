---
name: "mdk4"
categories:
  - 802-11
version: "4.2-2"
homepage: "https://github.com/aircrack-ng/mdk4"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-01-13"
build-system: make
meta-checked: "2026-11-09"
github-repo: "aircrack-ng/mdk4"
github-stars: 817
github-pushed: "2026-05-29"
github-archived: false
github-issues: 52
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# mdk4

> Wi-Fi proof-of-concept attack tool — aircrack-ng's continuation of mdk3 — with deauthentication, beacon flood, fuzzing, and 802.11s mesh attacks.

*No corpus evidence — derived from upstream documentation.*

## What it's for

mdk4 exploits common IEEE 802.11 protocol weaknesses by injecting frames through one or two monitor-mode interfaces (one receiving, one injecting). It supersedes mdk3 with the same attack families plus new ones: beacon flooding (b), authentication DoS (a), SSID probing/bruteforce (p), deauthentication/disassociation (d), TKIP Michael countermeasures (m), EAPOL start/logoff injection (e), 802.11s mesh attacks (s), WIDS confusion (w), a packet fuzzer (f), and PoC tests for Wi-Fi implementation vulnerabilities (x). It covers 2.4 and 5 GHz on Linux and supports IDS-evasion options. Like mdk3 it is a frame-injection/DoS tool — it neither captures nor cracks handshakes.

## Typical use cases

### Deauthenticate and disassociate clients

Disconnect all clients from the APs on the current channel.

```
sudo mdk4 wlan0mon d
```

Outcome: watches data traffic and sends deauthentication/disassociation frames at the observed stations until stopped; `-x` matches sequence numbers for full IDS stealth, `-s 500` caps the rate at 500 packets per second.

### Deauthenticate using two cards (smart injection)

Dedicate one interface to listening and one to injecting.

```
sudo mdk4 wlan0mon wlan1mon d
```

Outcome: runs the attack with the first interface as receiver and the second as injector, so targeting keeps working while both stay on the right channels.

### Beacon-flood with fake WEP/WPA networks

Advertise many counterfeit APs from a prepared SSID list.

```
sudo mdk4 wlan0mon b -f ssids.txt -w wta -s 100
```

Outcome: broadcasts beacons for each SSID in ssids.txt, flagging them as WEP, TKIP, and AES secured (`-w wta`) at 100 packets per second; nearby clients and scanners list the fake networks, and some drivers crash.

### Bruteforce a hidden SSID from a wordlist

Recover a cloaked network's name.

```
sudo mdk4 wlan0mon p -t AA:BB:CC:11:22:33 -f ssids.txt
```

Outcome: probes the AP with each candidate until it responds and prints the working SSID; `-b nlu` bruteforces digits, uppercase, and lowercase instead of using a wordlist.

### Look up an attack mode's options

List the flags of one attack mode.

```
mdk4 --help d
```

Outcome: prints the full option set for the deauthentication mode; `mdk4 --fullhelp` prints every mode's options.
