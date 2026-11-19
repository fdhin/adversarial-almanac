---
name: "fakeap"
categories:
  - blackarch-honeypot
version: "0.3.2"
homepage: "http://www.blackalchemy.to/project/fakeap/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# fakeap

> Black Alchemy's Fake AP floods the 802.11b airwaves with thousands of counterfeit beacon frames, hiding a real access point in plain sight.

## What it's for

Fake AP (Black Alchemy Weapons Lab, Perl, released 2002-2005) makes one wireless card appear to be thousands of access points: it emits beacon frames whose ESSID, source MAC, channel and WEP flags keep changing, fed by built-in word and vendor-OUI lists. The goal is confusion at survey time — wardrivers, NetStumblers and other scanning tools drown in phantom networks, so the one real AP becomes one tree in a forest; the vendor pitches it as usable in a honeypot or as part of a site security plan. It is a proof of concept of its era: it needs an Intersil Prism2/2.5/3 card driven by the old Host AP driver on Linux, and does not work with modern mac80211 hardware. This is the Perl `fakeap.pl`, unrelated to later same-named Python scripts that build phishing access points.

## Typical use cases

### Flood the airspace with rotating fake beacons

Saturate the band with counterfeit APs whose identities keep changing.

```
sudo fakeap --interface wlan0 --sleep 2
```

Outcome: the card transmits beacon frames whose ESSID and MAC change every 2 seconds (default 0.25 s), so scanning tools in range report a large and constantly shifting population of phantom access points.

### Advertise one stable fake network

Present a consistent decoy ESSID, optionally flagged as WEP.

```
sudo fakeap --interface wlan0 --essid CORP-GUEST --wep 0.5 --key wepkey12345
```

Outcome: every emitted beacon carries the ESSID CORP-GUEST, roughly half of them also advertise WEP with the given key, and the remaining options (channel, MAC, transmit power) are randomized as usual — a persistent fake target for survey tools.

*No corpus evidence — derived from upstream documentation.*
