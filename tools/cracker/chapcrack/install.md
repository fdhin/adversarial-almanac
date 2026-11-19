---
tool: "chapcrack"
build-system: "scripts"
upstream: "https://github.com/moxie0/chapcrack"
recipe: "blackarch"
source: "https://github.com/moxie0/chapcrack"
license: "GPL3"
blackarch-pkgver: "17.ae2827f"
---

# Installing chapcrack

> A tool for parsing and decrypting MS-CHAPv2 network handshakes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chapcrack) (version `17.ae2827f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S chapcrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/moxie0/chapcrack) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dpkt, python2-m2crypto, python2-passlib (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[chapcrack]] · [[BlackArch Tools]]

