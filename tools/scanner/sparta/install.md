---
tool: "sparta"
build-system: "scripts"
upstream: "http://sparta.secforce.com/"
recipe: "blackarch"
aur-package: "sparta"
source: "http://sparta.secforce.com/"
license: "GPL3"
blackarch-pkgver: "21.b0a4514"
---

# Installing sparta

> Python GUI application which simplifies network infrastructure penetration testing by aiding the penetration tester in the scanning and enumeration phase.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sparta) (version `21.b0a4514`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sparta    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sparta.secforce.com/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python2`
- ⚠️ Python 2 only deps: python2-elixir, python2-lxml, python2-pyqt4 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[sparta]] · [[BlackArch Tools]]

