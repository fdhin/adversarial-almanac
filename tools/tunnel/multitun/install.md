---
tool: "multitun"
build-system: "scripts"
upstream: "https://github.com/covertcodes/multitun"
recipe: "blackarch"
source: "https://github.com/covertcodes/multitun"
license: "GPL3"
blackarch-pkgver: "1.319a134"
---

# Installing multitun

> Tunnel arbitrary traffic through an innocuous WebSocket.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/multitun) (version `1.319a134`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S multitun    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/covertcodes/multitun) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-autobahn, python2-cryptography, python2-dpkt, python2-iniparse, python2-pycryptodome, python2-python-pytun, python2-streql, python2-twistedx, python2-typing (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[multitun]] · [[BlackArch Tools]]

