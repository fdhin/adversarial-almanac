---
tool: "honeypy"
build-system: "scripts"
upstream: "https://github.com/foospidy/HoneyPy"
recipe: "blackarch"
source: "https://github.com/foospidy/HoneyPy"
license: "GPL"
blackarch-pkgver: "600.6ca3d6e"
---

# Installing honeypy

> A low interaction Honeypot.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/honeypy) (version `600.6ca3d6e`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S honeypy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/foospidy/HoneyPy.git
cd HoneyPy
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-certifi, python2-dnslib, python2-requests, python2-twistedx, python2-urllib3 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[honeypy]] · [[BlackArch Tools]]

