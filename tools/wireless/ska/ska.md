---
name: "ska"
gentoo-category: "net-wireless"
version: "0.2"
homepage: "http://homepages.tu-darmstadt.de/~p_larbig/wlan/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ska

> Fakes 802.11 Shared Key Authentication on a WEP network using a previously recovered PRGA keystream.

## What it's for

ska (Fake Shared Key Authentication, by Pedro Larbig — k2wrlz, author
of mdk3) demonstrates that WEP Shared Key Authentication proves
nothing: given a PRGA keystream recovered beforehand (for example an
`replay_dec-*.xor` file from aireplay-ng's fragmentation attack), it
forges the encrypted challenge-response exchange, associates a fake
client to the access point without knowing the WEP key, and prints the
whole packet exchange ending in authentication and association
success. It is a 2006-era, single-purpose research tool: it does not
recover the WEP key itself, works only against shared-key WEP networks,
and equivalent functionality was later folded into the aircrack-ng
suite. The author's page (homepages.tu-darmstadt.de/~p_larbig/wlan/)
is no longer online; the source survives in distro recipes and web
archives.

## Typical use cases

### Fake shared-key authentication with a known keystream

Authenticate and associate to a WEP shared-key AP without the key.

```
sudo ska wlan0 "example-ssid" 00:11:22:33:44:55 66:77:88:99:AA:BB replay_dec-185527.xor
```

Outcome: six numbered steps scroll past — auth request, AP challenge,
encrypted challenge response, AP verdict ("Code 0 - Authentication
SUCCESSFUL"), association request and response ("Association
SUCCESSFUL"). Arguments are the raw-packet interface (wlanX, rausbX or
athX), ESSID, BSSID, the fake client MAC and the PRGA file. The usage
banner compiled into ska.c omits the leading interface argument; the
five-argument form above is the one that actually runs.

*No corpus evidence — derived from upstream sources (ska 0.2 tarball: README transcript of a complete run and ska.c argument handling; author page via web archive).*
