---
tool: "mwebfp"
build-system: "scripts"
upstream: "https://github.com/falcon-lnhg/mwebfp"
recipe: "blackarch"
source: "https://github.com/falcon-lnhg/mwebfp"
license: "GPL3"
blackarch-pkgver: "16.a800b98"
---

# Installing mwebfp

> Mass Web Fingerprinter.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mwebfp) (version `16.a800b98`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S mwebfp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/falcon-lnhg/mwebfp) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `cutycapt`
  - `nmap`
- ⚠️ Python 2 only deps: python2-python-nmap, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[mwebfp]] · [[BlackArch Tools]]

