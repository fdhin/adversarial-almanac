---
tool: "kacak"
build-system: "scripts"
upstream: "https://github.com/galkan/kacak"
recipe: "blackarch"
source: "https://github.com/galkan/kacak"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing kacak

> Tools for penetration testers that can enumerate which users logged on windows system.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kacak) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S kacak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/galkan/kacak) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `metasploit`
  - `python2`
- ⚠️ Python 2 only deps: python2-lxml, python2-python-nmap (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[kacak]] · [[BlackArch Tools]]

