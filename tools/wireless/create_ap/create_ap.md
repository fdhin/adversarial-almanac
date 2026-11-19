---
name: "create_ap"
categories:
  - blackarch-wireless
version: "265.462c09f"
homepage: "https://github.com/oblique/create_ap"
packaged-by: [blackarch, aur]
blackarch-builddate: "2021-01-23"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "oblique/create_ap"
github-stars: 4511
github-pushed: "2023-12-13"
github-archived: true
github-issues: 206
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# create_ap

> oblique's bash script that turns a Wi-Fi interface into a NATed or bridged software access point in one command.

## What it's for

create_ap is a shell script that orchestrates hostapd, dnsmasq and iptables to bring up a software AP from a Wi-Fi interface with one command: share an uplink via NAT or bridging (or run with no Internet sharing at all), including the same-interface trick where a single Wi-Fi card stays associated to the uplink while hosting the AP. It supports WPA/WPA2 passphrases or open networks, hidden SSIDs, client isolation, 802.11n/ac options, a chosen gateway IP, and SSID/passphrase via pipe instead of arguments. It has no attack logic — for rogue-AP work see berate_ap, a fork of this script wired to hostapd-mana. The upstream is unmaintained (archived); continued forks are linux-wifi-hotspot (GUI) and linux-router (adds wired sharing and transparent-proxy sharing).

## Typical use cases

### Stand up a WPA2 AP sharing an uplink via NAT

```
create_ap wlan0 eth0 MyAccessPoint MyPassPhrase
```

Outcome: the AP MyAccessPoint appears on wlan0 with clients NATed behind eth0; hostapd, dnsmasq and iptables are configured automatically and torn down cleanly on exit. Omit the passphrase for an open network.

### Share an uplink from the same single card

```
create_ap wlan0 wlan0 MyAccessPoint MyPassPhrase
```

Outcome: the same interface keeps its uplink association and simultaneously hosts the AP on a virtual interface — for machines with only one radio.

### Run isolated (no Internet) or bridge clients onto the LAN

```
create_ap -n wlan0 MyAccessPoint MyPassPhrase
create_ap -m bridge wlan0 eth0 MyAccessPoint MyPassPhrase
```

Outcome: `-n` hosts a standalone AP with no upstream (clients get DHCP from create_ap's gateway IP); `-m bridge` places associated clients directly on eth0's LAN segment — for an existing bridge device, pass `br0` as the second interface instead.

### Harden a shared AP

```
create_ap --hidden --isolate-clients --ieee80211n --ht_capab '[HT40+]' wlan0 eth0 MyAccessPoint MyPassPhrase
```

Outcome: broadcasts a hidden SSID, blocks client-to-client traffic (the setting you want on semi-public APs), and enables 802.11n with 40 MHz channels; each flag is independent and combines with any of the sharing modes above.

*No corpus evidence — derived from upstream documentation.*
