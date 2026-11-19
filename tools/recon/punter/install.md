---
tool: "punter"
build-system: "scripts"
upstream: "https://github.com/nethunteros/punter"
recipe: "blackarch"
source: "https://github.com/nethunteros/punter"
license: "custom:unknown"
blackarch-pkgver: "45.97b7bed"
---

# Installing punter

> Hunt domain names using DNSDumpster, WHOIS, Reverse WHOIS, Shodan, Crimeflare.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/punter) (version `45.97b7bed`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S punter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nethunteros/punter) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-api-dnsdumpster, python2-argparse, python2-beautifulsoup4, python2-configparser, python2-dnspython, python2-future, python2-ipaddr, python2-ipwhois, python2-lxml, python2-python-whois, python2-requests, python2-shodan (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[punter]] · [[BlackArch Tools]]

