---
tool: "0trace"
build-system: "scripts"
upstream: "http://jon.oberheide.org/0trace/"
recipe: "blackarch"
source: "http://jon.oberheide.org/0trace/"
license: "GPL3"
blackarch-pkgver: "1.5"
kali-package: "0trace"
kali-version: "0.01-3kali6"
---

# Installing 0trace

> A hop enumeration tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/0trace) (version `1.5`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S 0trace    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install 0trace    # Kali package version 0.01-3kali6
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://jon.oberheide.org/0trace/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
- ⚠️ Python 2 only deps: python2-dpkt, python2-pydnet, python2-pypcap, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[0trace]] · [[BlackArch Tools]]

