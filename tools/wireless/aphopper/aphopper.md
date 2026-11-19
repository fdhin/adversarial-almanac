---
name: "aphopper"
categories:
  - blackarch-wireless
version: "0.3"
homepage: "http://aphopper.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2017-06-02"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "BlackArch/aphopper"
github-stars: 3
github-pushed: "2014-03-15"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aphopper

> Automatically re-associates a wireless card across nearby access points until it lands on one with working DHCP and internet.

## What it's for

AP Hopper (2002) keeps a Linux wireless interface usable in an environment of many open APs: it watches link quality through `/proc/net/wireless`, and whenever the link degrades or the current AP proves useless it sets the ESSID to `any` to associate with the next reachable AP, requests a lease with `dhcpcd`, and verifies real internet connectivity by opening a TCP connection to a well-known web host. APs that pass are appended to a good-list and broken ones to a bad-list, so the tool converges on usable networks. It is wireless-extensions-era code — no WEP/WPA key handling, no cracking, and expect it to interact poorly with modern mac80211 drivers; today it is mainly of historical interest.

## Typical use cases

### Hop across access points on one card

Let the tool cycle APs, test each, and keep the working ones.

```
sudo aphopper wlan0
```

Outcome: prints `Running on interface wlan0`, then continuously prints link quality and association activity; each AP that yields a DHCP lease and internet is appended to `/root/.ssid_good`, each failure to `/root/.ssid_bad`, and the loop keeps hopping whenever the current AP stops delivering.

*No corpus evidence — derived from upstream documentation.*
