---
name: "routerkeygenpc"
categories:
  - wireless
version: "1.1.0+git20190721.c1f1665-0parrot1"
homepage: "https://github.com/routerkeygen/routerkeygenPC"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: cmake
meta-checked: "2026-11-09"
github-repo: "routerkeygen/routerkeygenPC"
github-stars: 313
github-pushed: "2022-09-02"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# routerkeygenpc

> Computes the default WPA/WEP keys of dozens of home router models from the network name and/or MAC address.

## What it's for

RouterKeygen (PC port) calculates the factory-default Wi-Fi passwords
that ISPs shipped with their routers: Thomson-based models (Thomson,
SpeedTouch, Orange, Infinitum, BBox, …), DLink (some models), Pirelli
Discus, Eircom, Verizon FiOS, Alice AGPF, FASTWEB Pirelli/Telsey,
Huawei, `Wlan_XXXX`/`Jazztel_XXXX`, EasyBox/Arcor/Vodafone, and many
more. Where the default key is a deterministic function of the SSID or
the router's MAC address, this CLI prints every candidate in seconds,
which you then test against the live network. It only derives keys
computable from public information — it performs no handshake capture
or cryptanalysis, so a changed password is out of reach.

## Typical use cases

### List candidate keys for a network by SSID

Compute the default-password candidates for a target network name.

```
routerkeygen-cli --ssid "WLAN_1234AB"
```

Outcome: every candidate key the implemented algorithms produce for
that SSID, one per line — try each when associating or against a
captured handshake.

### Derive keys that also need the MAC address

Some generators key off the access point's MAC rather than the SSID.

```
routerkeygen-cli --ssid "FASTWEB-1A2B3C" --mac 00:11:22:33:44:55
```

Outcome: additional candidates from the generators that combine SSID
and BSSID (e.g. the Pirelli/FASTWEB families); without `--mac` those
algorithms produce nothing.

### Show which algorithm produced each candidate

Map each printed key back to its key generator.

```
routerkeygen-cli --ssid "Thomson08F2A1" --kg
```

Outcome: lines in `keygen-name:candidate` form instead of bare keys,
showing which router family matched and how plausible the result is.
`-q`/`--quiet` does the opposite, printing only the calculated keys.

*No corpus evidence — derived from upstream documentation (CLI options `-s/--ssid`, `-m/--mac`, `-k/--kg`, `-q/--quiet` read from `cli/routerkeygen-cli.cpp`; supported router list from the upstream README).*
