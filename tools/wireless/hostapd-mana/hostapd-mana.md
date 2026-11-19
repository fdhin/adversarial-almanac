---
name: "hostapd-mana"
categories:
  - blackarch-wireless
version: "2.6.5.r20.g8853d5a"
homepage: "https://github.com/sensepost/hostapd-mana"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-09-26"
build-system: make
license: custom:BSD/GPL v2 dual
meta-checked: "2026-11-09"
github-repo: "sensepost/hostapd-mana"
github-stars: 636
github-pushed: "2026-10-07"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1188
gh-activity-checked: "2026-11-09"
---

# hostapd-mana

> SensePost's patched hostapd: a karma-style rogue access point with WPE-style EAP credential harvesting and WPA handshake capture built in.

## What it's for

hostapd-mana is hostapd with SensePost's mana patches (first presented at Defcon 22 by Dominic White and Ian de Villiers). With `enable_mana=1` the access point answers probe requests for any network it has already seen, so clients that probe for their remembered networks associate with you — the karma attack without a separate Metasploit stack. `mana_wpe=1` turns the built-in EAP server into a credential harvester against WPA-Enterprise clients (PEAP/EAP-TTLS challenge-response pairs are appended to the `mana_credout` file), `mana_wpaout` records WPA handshakes straight to a hashcat-ready hccapx file, and `mana_eapsuccess`/`mana_eaptls` cover clients that need a successful EAP result before they talk. It is the engine SensePost's `berate_ap` orchestrates (see the [[berate_ap]] note) and the successor-in-spirit to Foundstone's [[hostapd-wpe]]; the wider mana toolkit (SSL certificate tooling, firewall helpers, scripts) ships separately. You write a hostapd-style config and run the patched binary as root — it is not a one-liner AP creator.

## Typical use cases

### Run a karma rogue AP that answers every probe

```
hostapd-mana mana.conf
```

with at least this in `mana.conf`:

```
interface=wlan0
ssid=FreeWiFi
channel=6
enable_mana=1
```

Outcome: brings up the AP on wlan0; every client probing for a network name this AP has observed gets an affirmative probe response, so remembered-network clients associate to you. Watch the console for associations; `mana_outfile` can mirror mana's output to a file.

### Advertise every SSID ever seen (loud mode)

```
mana_loud=1
```

Outcome: with loud mode set in the config, the AP beacons *all* SSIDs it has learned in addition to answering probes — attracting clients that are not actively probing but are scanning for known names; noisy by design, so expect attention.

### Harvest WPA-Enterprise credentials

```
enable_mana=1
mana_wpe=1
mana_credout=hostapd.credout
eap_user_file=hostapd.eap_user
```

Outcome: the rogue AP advertises (or answers probes for) an 802.1X network and completes PEAP/EAP-TTLS handshakes against its own EAP server; each client's username plus MSCHAPv2 challenge/response is appended to `hostapd.credout` for offline cracking — supply matching certificates so the client's supplicant accepts the TLS tunnel.

### Keep Enterprise clients connected after the harvest

```
mana_eapsuccess=1
mana_eaptls=1
```

Outcome: the EAP server returns EAP-Success (and full EAP-TLS instead of failing mid-handshake), so compliant clients believe authentication worked and stay associated — necessary when the follow-up is a captive portal or client-side attack rather than just credential collection.

### Capture WPA-PSK handshakes straight to hashcat format

```
enable_mana=1
mana_wpaout=hostapd.hccapx
```

Outcome: any WPA handshake completed by an associating client is appended to `hostapd.hccapx` in hashcat's hccapx format — crack it directly with `hashcat -m 2500` (mode 22000 for converted captures) without running a converter first.

### Restrict which SSIDs mana will answer

```
mana_ssid_filter_file=hostapd.ssid_filter
mana_ssid_filter_type=1
```

Outcome: probes are matched against the SSIDs listed (one per line) in the filter file, and the filter type selects whether the list is treated as an allow or deny list — use it to keep the rogue AP plausibly scoped to the networks you are actually authorized to impersonate.

*No corpus evidence — derived from upstream documentation (config options verified against the packaged source's hostapd.conf and config parser).*
