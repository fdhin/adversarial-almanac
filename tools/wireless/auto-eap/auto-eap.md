---
name: "auto-eap"
categories:
  - blackarch-wireless
version: "18.ee36d37"
homepage: "https://github.com/Tylous/Auto_EAP"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Tylous/Auto_EAP"
github-stars: 58
github-pushed: "2020-03-19"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# auto-eap

> Password-sprays WPA-Enterprise (802.1X/EAP) networks by scripting wpa_supplicant authentication attempts.

## What it's for

Auto_EAP (Tylous) automates credential guessing against EAP-secured wireless networks — the kind whose authentication is checked by a RADIUS server against Active Directory. It drives a private `wpa_supplicant` instance through the wpaspy library, trying every username in a file against one password and reporting each attempt's result, pacing the attempts with the stated intent of not tripping account lock-outs. Reach for it when auditing password policy on an 802.1X network you are authorized to test; it targets enterprise EAP types (e.g. PEAP under WPA-EAP key management) and is not a WPA-PSK or WPS tool.

## Typical use cases

### Spray one password across a username list

Test a single common password against every account name on a WPA-Enterprise SSID.

```
sudo auto-eap -i wlan0 -s CORP-EAP -K WPA-EAP -E PEAP -U users.txt -p Password1
```

Outcome: initializes its private wpa_supplicant, then prints one line per candidate — `Trying Username <user> with Password Password1: SUCCESS` or `FAILED` — ending with `Completed`; every `SUCCESS` is a domain credential the RADIUS backend accepted, and the flags map to interface (`-i`), SSID (`-s`), key management (`-K`), EAP type (`-E`), username file (`-U`) and password (`-p`).

*No corpus evidence — derived from upstream documentation.*
