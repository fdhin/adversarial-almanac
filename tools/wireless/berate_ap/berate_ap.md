---
name: "berate_ap"
categories:
  - blackarch-wireless
version: "v0.4.6.r100.ged4ba9b"
homepage: "https://github.com/sensepost/berate_ap"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-06"
build-system: scripts
license: BSD-2-Clause
meta-checked: "2026-11-09"
github-repo: "sensepost/berate_ap"
github-stars: 250
github-pushed: "2026-08-27"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# berate_ap

> A create_ap fork that stands up hostapd-mana rogue APs in one command — karma probing, WPA-Enterprise credential harvesting and WPA handshake capture.

## What it's for

berate_ap is SensePost's bash orchestration layer for rogue access points: it is a modified create_ap that runs hostapd-mana with multiple options instead of plain hostapd. One command brings up the AP, wires NAT or bridged Internet sharing, generates certificates, and exposes mana's attack modes — karma-style probe-response, mana-WPE EAP credential harvesting against WPA-Enterprise clients, and WPA handshake capture to a hashcat-ready hccapx file. It also stands up genuine WPA/WPA2-PSK and 802.1X (EAP) networks with a built-in or remote RADIUS server, which is what distinguishes it from the unmaintained original create_ap. It requires hostapd-mana in PATH and root; it does not replace hand-written hostapd configs for exotic setups — its value is the orchestrated defaults.

## Typical use cases

### Run a mana rogue AP that answers every probe

```
berate_ap --eap --mana wlan0 eth0 MyAccessPoint
```

Outcome: starts a hostapd-mana AP on wlan0 sharing eth0's Internet via NAT; clients that probe for their remembered networks get affirmative answers and associate to you. EAP credential harvesting is off by default here — mana's default eap user file is used.

### Harvest WPA-Enterprise credentials (mana WPE)

```
berate_ap --eap --mana-wpe --mana-credout /tmp/hostapd.credout wlan0 eth0 MyAccessPoint
```

Outcome: enables mana's WPE-style attacks; on first EAP start the script prompts for certificate fields and generates the certificate. Challenge/response material from connecting 802.1X clients is appended to the credout file (default /tmp/hostapd.credout); related toggles are `--mana-eapsuccess` and `--mana-eaptls`.

### Capture WPA handshakes with a PSK clone

```
berate_ap --mana-wpa -n wlan0 TargetNetworkName "ANYPASSWORD"
```

Outcome: runs mana in WPA-capture mode (mana attacks disabled in this mode): a PSK clone of the named network that accepts any password, run with no Internet sharing (`-n`); each client handshake is appended to /tmp/hostapd.hccapx (override with `--mana-wpaout`), ready for hashcat.

### Loud mode and bridged clients

```
berate_ap --eap --mana --mana-loud -m bridge wlan0 eth0 MyAccessPoint
```

Outcome: mana advertises every SSID it has ever seen (loud mode) and associated clients are bridged directly onto eth0's network instead of NATed; swap `-m bridge` for `-n` to run the AP with no upstream Internet at all.

### Stand up a genuine WPA-Enterprise test network

```
berate_ap --eap --eap-user-file /tmp/users.eap_hosts --eap-cert-path /tmp/certificates wlan0 eth0 MyAccessPoint
```

Outcome: creates an 802.1X AP backed by hostapd's built-in EAP server using the supplied user file and certificates; for an external AAA backend use `--radius-server 198.51.100.10:1812 --radius-secret=Password1` instead.

*No corpus evidence — derived from upstream documentation.*
