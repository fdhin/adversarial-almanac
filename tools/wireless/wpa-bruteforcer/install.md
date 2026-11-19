---
tool: "wpa-bruteforcer"
build-system: "scripts"
upstream: "https://github.com/SYWorks/wpa-bruteforcer"
recipe: "blackarch"
source: "https://github.com/SYWorks/wpa-bruteforcer"
license: "GPL2"
blackarch-pkgver: "4.d5f8586"
---

# Installing wpa-bruteforcer

> Attacking WPA/WPA encrypted access point without client.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wpa-bruteforcer) (version `4.d5f8586`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S wpa-bruteforcer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/SYWorks/wpa-bruteforcer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wpa-bruteforcer]] · [[BlackArch Tools]]

