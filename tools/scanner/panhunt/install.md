---
tool: "panhunt"
build-system: "scripts"
upstream: "https://github.com/Dionach/PANhunt"
recipe: "blackarch"
source: "https://github.com/Dionach/PANhunt"
license: "GPL3"
blackarch-pkgver: "63.ec87e88"
---

# Installing panhunt

> Searches for credit card numbers (PANs) in directories.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/panhunt) (version `63.ec87e88`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S panhunt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Dionach/PANhunt.git
cd PANhunt
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-colorama, python2-progressbar (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[panhunt]] · [[BlackArch Tools]]

