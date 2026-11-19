---
name: "mdk3"
categories:
  - blackarch-wireless
version: "v6"
homepage: "https://aspj.aircrack-ng.org/"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2024-12-06"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "aircrack-ng/mdk3"
github-stars: 48
github-pushed: "2018-02-25"
github-archived: true
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.9
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mdk3

> Proof-of-concept 802.11 attack tool that floods Wi-Fi networks with deauthentication, beacon, and authentication frames.

*No corpus evidence — derived from upstream documentation.*

## What it's for

mdk3 exploits common IEEE 802.11 protocol weaknesses by injecting crafted management frames through a monitor-mode interface. It provides denial-of-service attacks (deauthentication "amok" mode, authentication floods, TKIP Michael shutdown), reconnaissance aids (hidden-SSID probing and bruteforcing), and disruptive tests (beacon flooding, WIDS/WDS confusion, EAPOL flooding). It is purely a frame-injection tool: it does not capture or crack handshakes itself. mdk3 is the predecessor of mdk4, which continues the same tool under the aircrack-ng organization — prefer mdk4 on modern systems and drivers.

## Typical use cases

### Deauthenticate every client in range

Kick all stations off the APs your card can reach on the current channel.

```
sudo mdk3 wlan0mon d
```

Outcome: continuously sends deauthentication and disassociation frames to every AP and client observed; stop with Ctrl+C. Add `-c 1,6,11` to hop channels or `-w whitelist.txt` to spare specific MAC addresses.

### Beacon-flood fake access points

Fill nearby clients' network lists with counterfeit APs.

```
sudo mdk3 wlan0mon b -n "FreeWiFi" -m -s 200
```

Outcome: emits beacons at 200 packets per second advertising SSID "FreeWiFi" with plausible vendor MACs (`-m` pulls from the built-in OUI database); network scanners and some drivers can hang or crash under the flood.

### Authentication denial of service against one AP

Bury a single access point in fake client associations.

```
sudo mdk3 wlan0mon a -a AA:BB:CC:11:22:33
```

Outcome: floods the target AP with authentication frames from spoofed client MACs; many APs freeze or reset under the load until the flood stops.

### Bruteforce a hidden SSID

Recover the SSID of a cloaked access point from a wordlist.

```
sudo mdk3 wlan0mon p -t AA:BB:CC:11:22:33 -f ssids.txt
```

Outcome: probes the target AP with each candidate SSID and reports when it answers, revealing the hidden name; `-b <charset>` switches to full bruteforce over the given character set, recommended for short SSIDs only.

### Force a TKIP AP offline (Michael countermeasures)

Make a TKIP-protected AP shut itself down for a minute at a time.

```
sudo mdk3 wlan0mon m -t AA:BB:CC:11:22:33 -j
```

Outcome: provokes the Michael countermeasures reaction so the AP halts traffic for about one minute per triggered burst, repeating until stopped; `-j` selects the QoS variant that needs only a few packets.
