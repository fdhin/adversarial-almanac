---
name: "mana"
categories:
  - blackarch-wireless
version: "68.56bcfcd"
homepage: "https://github.com/sensepost/mana"
packaged-by: [blackarch]
blackarch-builddate: "2015-06-15"
build-system: make
license: BSD, GPL3
meta-checked: "2026-11-09"
github-repo: "sensepost/mana"
github-stars: 1118
github-pushed: "2018-08-21"
github-archived: false
github-issues: 49
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mana

> Rogue-access-point toolkit wrapping hostapd-mana with MitM tooling.

## What it's for

The MANA toolkit (Dominic White & Ian de Villiers, SensePost, DEF CON
22) bundles everything for a rogue-AP / "evil twin" engagement:
hostapd-mana — a hostapd with improved KARMA attacks — as the access
point, plus controller scripts layering on DHCP, NAT or a fake Internet
with captive portal, and MitM tooling (sslstrip-hsts, sslsplit,
dns2proxy, firelamb cookie harvesting, crackapd for auto-cracking EAP
credentials). SensePost has deprecated the toolkit in favour of
bettercap and berate_ap; the actively maintained core is
[[hostapd-mana]]. The start scripts kill NetworkManager and assume the
Wi-Fi card defaults to wlan0 — edit them to match your hardware. Only
use against networks and clients you are authorized to test.

## Typical use cases

### Start a full MitM rogue AP

Bring up the complete attack with all MitM components.

```
/usr/share/mana-toolkit/run-mana/start-nat-full.sh
```

Outcome: hostapd-mana runs MANA/KARMA on the configured interface,
associated clients are NATed to an upstream link through the sslstrip /
sslsplit / dns2proxy chain, and captured traffic plus firelamb's
harvested cookies land under /var/lib/mana-toolkit.

### Start a plain rogue AP without extras

Association testing without the MitM stack.

```
/usr/share/mana-toolkit/run-mana/start-nat-simple.sh
```

Outcome: MANA access point with DHCP and NAT only — no firelamb,
sslstrip or sslsplit — useful for simply counting and identifying
clients that auto-connect.

### Run a "fake Internet" access point

Serve local content when there is no upstream.

```
/usr/share/mana-toolkit/run-mana/start-noupstream.sh
```

Outcome: no upstream connectivity; connected clients are served the
captive portal and fake web content from the toolkit's Apache vhosts —
for locations where victims leave Wi-Fi on but there is no real
Internet to NAT to.

### Attack EAP/802.1X clients

Target enterprise supplicants.

```
/usr/share/mana-toolkit/run-mana/start-noupstream-eap.sh
```

Outcome: MANA in noupstream mode with the EAP attack enabled —
clients authenticating against the fake AP hand EAP credentials to
crackapd, which offloads cracking and feeds working credentials back
into the hostapd config.

*No corpus evidence — derived from upstream documentation.*
