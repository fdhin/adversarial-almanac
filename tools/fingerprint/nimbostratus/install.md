---
tool: "nimbostratus"
build-system: "scripts"
upstream: "https://github.com/andresriancho/nimbostratus"
recipe: "blackarch"
source: "https://github.com/andresriancho/nimbostratus"
license: "GPL3"
blackarch-pkgver: "54.c7c206f"
---

# Installing nimbostratus

> Tools for fingerprintinging and exploiting Amazon cloud infrastructures.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nimbostratus) (version `54.c7c206f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S nimbostratus    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/andresriancho/nimbostratus) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-boto, python2-httpretty, python2-nose, python2-requests, python2-six (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[nimbostratus]] · [[BlackArch Tools]]

