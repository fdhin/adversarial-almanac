---
tool: "dns2geoip"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/118036/DNS-2-GeoIP.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/118036/DNS-2-GeoIP.html"
license: "unknown"
blackarch-pkgver: "0.1"
---

# Installing dns2geoip

> A simple python script that brute forces DNS and subsequently geolocates the found subdomains.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dns2geoip) (version `0.1`, license unknown).

## Arch / BlackArch

```bash
sudo pacman -S dns2geoip    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/118036/DNS-GeoIP.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dnspython, python2-geoip, python2-simplekml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[dns2geoip]] · [[BlackArch Tools]]

