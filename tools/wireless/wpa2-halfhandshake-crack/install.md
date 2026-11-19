---
tool: "wpa2-halfhandshake-crack"
build-system: "scripts"
upstream: "https://github.com/dxa4481/WPA2-HalfHandshake-Crack"
recipe: "blackarch"
source: "https://github.com/dxa4481/WPA2-HalfHandshake-Crack"
license: "MIT"
blackarch-pkgver: "29.3f42124"
---

# Installing wpa2-halfhandshake-crack

> A POC to show it is possible to capture enough of a handshake with a user from a fake AP to crack a WPA2 network without knowing the passphrase of the actual AP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wpa2-halfhandshake-crack) (version `29.3f42124`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S wpa2-halfhandshake-crack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/dxa4481/WPA2-HalfHandshake-Crack.git
cd WPA2-HalfHandshake-Crack
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pbkdf2_ctypes, python2-pcapfile, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wpa2-halfhandshake-crack]] · [[BlackArch Tools]]

