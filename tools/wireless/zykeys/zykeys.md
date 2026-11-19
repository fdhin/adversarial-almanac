---
name: "zykeys"
categories:
  - blackarch-wireless
version: "0.1"
homepage: "http://packetstormsecurity.com/files/119156/Zykeys-Wireless-Tool.html"
packaged-by: [blackarch]
blackarch-builddate: "2023-03-01"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "cmpxchg8/zykeys"
github-stars: 5
github-pushed: "2014-01-08"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# zykeys

> PoC generator that demonstrates how default wireless settings (WEP/WPA2 keys, SSID, TR-069 ACS credentials) are derived on ZyXEL routers.

## What it's for

zykeys (by Kevin Devine and Damien O'Reilly, 2012) is a small C demonstration of the default-configuration algorithms used on some ZyXEL router models (the reference implementation targets the P-660HN-T1A as shipped by the Irish ISP eircom, OUI 00:13:49). It shows that from identifiers printed on the device — MAC address and serial number — an attacker can recompute everything the ISP pre-configured: the 64- and 128-bit WEP keys, the numeric SSID suffix, the WPA2-PSK, and the TR-069 management credentials (ACS username and password, derived with a fixed ISP secret). It is a research PoC, not a scanner: it takes no command-line arguments and computes output for example identifiers compiled into `main()`; to derive keys for a specific device you edit the `gen_*()` calls and the constants (`SSID_PREFIX`, `ISP_OUI`, `ACS_SECRET`) and rebuild. Unchanged non-default configurations are out of scope.

## Typical use cases

### Run the derivation demo

```
zykeys
```

Outcome: prints the derived 64-bit and 128-bit WEP keys, the SSID digits, the WPA2-PSK and the ACS username/password for the built-in example MAC and serial values — a compact demonstration of how much of a factory ZyXEL config is predictable from the label alone.

*No corpus evidence — derived from upstream documentation.*
