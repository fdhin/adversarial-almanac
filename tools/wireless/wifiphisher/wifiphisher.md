---
name: "wifiphisher"
categories:
  - blackarch-wireless
version: "800.4ae3365"
homepage: "https://github.com/wifiphisher/wifiphisher"
packaged-by: [blackarch]
blackarch-builddate: "2026-06-01"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "wifiphisher/wifiphisher"
github-stars: 14948
github-pushed: "2026-05-22"
github-archived: false
github-issues: 354
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# wifiphisher

> Rogue-AP framework that forces Wi-Fi clients onto an access point it controls, then phishes credentials with victim-tailored fake pages.

## What it's for

Wifiphisher automates evil-twin engagements end to end: it stands up a rogue access point through its bundled [[roguehostapd]] fork of hostapd, knocks victims off the legitimate network with forged deauthentication/disassociation frames, and lures them into associating via Evil Twin (cloned network), KARMA (answers every probe) or Known Beacons (broadcasts a dictionary of common ESSIDs clients have joined before). Once a victim is connected, it serves one of the built-in phishing scenarios — a router "firmware upgrade" page to harvest the WPA/WPA2 pre-shared key, an OAuth login page for third-party credentials, or a plugin-update page that serves a payload — customized to the victim's platform based on beacon and User-Agent fingerprints. It runs as a guided TUI, verifies a harvested PSK against a captured WPA handshake, and is extensible with Python modules and custom phishing pages. Requirements: Linux (Kali is the reference platform), one wireless adapter supporting AP+monitor mode and injection (a second one for the deauth/extensions side is better). It does not crack anything — its value is the man-in-the-middle position and credential capture, not offline key recovery.

## Typical use cases

### Guided attack from the TUI

Let the tool pick interfaces and walk through target and scenario selection.

```
sudo wifiphisher
```

Outcome: Wifiphisher identifies suitable adapters, then interactively lists nearby ESSIDs and the available phishing scenarios; after selection it brings up the rogue AP (Evil Twin + KARMA by default), starts deauthenticating clients from the real network, and shows credentials live in the TUI as victims connect and submit the fake form — Ctrl-C tears the AP down.

### Harvest the WPA PSK with handshake verification

Clone a target network and validate the phished key offline.

```
sudo wifiphisher -aI wlan0 -eI wlan1 -p firmware-upgrade --handshake-capture handshake.pcap
```

Outcome: spawns the rogue AP on wlan0 while wlan1 runs the extensions side (deauthentication), prompts for the target network, and serves the firmware-upgrade page asking for the Wi-Fi password; when a victim submits it, the key is checked against the WPA handshake in handshake.pcap (requires cowpatty) so only verified-correct PSKs are reported as success.

### Open network with Known Beacons association

Attract clients in public areas with a free-looking network.

```
sudo wifiphisher --essid "FREE WI-FI" -p oauth-login -kB
```

Outcome: broadcasts an open AP named FREE WI-FI and additionally floods beacons for many common ESSIDs (Known Beacons) so stations that probe for known networks auto-associate; connected victims get the OAuth-login scenario page, and captured third-party credentials appear in the session output.

### Payload delivery on a network with a disclosed PSK

Recreate a password-protected evil twin where the real key is public.

```
sudo wifiphisher --essid CONFERENCE_WIFI -p plugin_update -pK Password1
```

Outcome: brings up the rogue AP with ESSID CONFERENCE_WIFI protected by the given passphrase (victims can join using the publicly known key), and serves the plugin-update page from which victims download the prepared payload — the payload path is enabled with `--payload-path`.

*No corpus evidence — derived from upstream documentation (flags verified against the wifiphisher/wifiphisher README options table and the packaged argparse definitions; the README's legacy `-jI` example flag no longer exists in the current source and was replaced by `-eI`).*
