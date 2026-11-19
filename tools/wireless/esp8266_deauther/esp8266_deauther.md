---
name: "esp8266_deauther"
categories:
  - wireless
version: "git (2024-08-14)"
homepage: "https://github.com/spacehuhntech/esp8266_deauther"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-09"
github-repo: "spacehuhntech/esp8266_deauther"
github-stars: 15096
github-pushed: "2024-08-14"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 99
---

# esp8266_deauther

> ESP8266 firmware for testing 802.11 deauthentication, beacon flood and probe flood attacks from a cheap microcontroller.

## What it's for

ESP8266 Deauther (Spacehuhn / Spacehuhn Technologies) is firmware you flash onto an ESP8266 board, not a program you run on a computer. Once flashed it turns the board into a standalone 2.4 GHz testing device: it scans for access points and client stations, can deauthenticate selected targets, and can flood scanners with dozens of fake networks via beacon and probe frames. It is controlled either through a serial console (commands documented in the repository's `serialcommands.md`) or through a built-in web interface served by the board itself. The project is explicitly a proof of concept for testing your own networks and devices — many client chipsets now ignore legacy deauth frames, and attacks time out automatically to discourage abuse. It does not capture or crack credentials of any kind.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Scan for targets and select one

From a serial terminal attached to the board, discover nearby access points and mark one as the attack target.

```
scan aps -t 15
show aps
select aps 1
```

Outcome: scans for access points for 15 seconds, lists them with their IDs, and selects ID 1; `scan stations` does the same for client devices, and `show selected` confirms the current target list.

### Clone an SSID and flood beacons

Fill nearby Wi-Fi scanners with copies of a network name to make the real one hard to pick out.

```
add ssid -ap 1 -cl 10
attack beacon -t 60
```

Outcome: copies the SSID of scanned AP 1 ten times into the SSID list and then broadcasts beacon frames for all listed SSIDs for 60 seconds; `add ssid MyNet -wpa2` adds an arbitrary name advertised as WPA2 instead.

### Deauthenticate a selected target

Test whether your own devices still succumb to spoofed deauthentication frames.

```
select aps 1
attack deauth
```

Outcome: sends deauth frames at stations associated with the selected AP until the configured timeout; `attack deauthall` targets every station in range instead (the two modes are mutually exclusive), and `stop attack` halts it early.

### Start the on-device web interface

Drive scan, selection and attacks from a browser instead of the serial console.

```
startap -s pwned -pswd deauther
```

Outcome: the board brings up its own access point; join it and open http://192.168.4.1 to scan, select targets, manage the SSID list, and start or stop the same attacks from a web page.
