---
tool: "anti-xss"
build-system: "scripts"
upstream: "https://github.com/lewangbtcc/anti-XSS"
recipe: "blackarch"
source: "https://github.com/lewangbtcc/anti-XSS"
license: "MIT"
blackarch-pkgver: "v0.1.alpha.r69.g2725dc9"
---

# Installing anti-xss

> A XSS vulnerability scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/anti-xss) (version `v0.1.alpha.r69.g2725dc9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S anti-xss    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lewangbtcc/anti-XSS
cd anti-XSS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-reportlab, python2-tornado (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[anti-xss]] · [[BlackArch Tools]]

