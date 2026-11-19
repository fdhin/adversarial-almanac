---
tool: "inguma"
build-system: "scripts"
upstream: "http://inguma.sourceforge.net"
recipe: "blackarch"
source: "http://inguma.sourceforge.net"
license: "GPL"
blackarch-pkgver: "0.1.1"
---

# Installing inguma

> A free penetration testing and vulnerability discovery toolkit entirely written in python. Framework includes modules to discover hosts, gather information about, fuzz targets, brute force usernames and passwords, exploits, and a disassembler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/inguma) (version `0.1.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S inguma    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://inguma.sourceforge.net) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-paramiko, python2-pyqt (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[inguma]] · [[BlackArch Tools]]

