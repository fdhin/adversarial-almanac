---
tool: "kippo"
build-system: "scripts"
upstream: "https://github.com/desaster/kippo"
recipe: "blackarch"
source: "https://github.com/desaster/kippo"
license: "GPL"
blackarch-pkgver: "287.b9eb06a"
---

# Installing kippo

> A medium interaction SSH honeypot designed to log brute force attacks and most importantly, the entire shell interaction by the attacker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kippo) (version `287.b9eb06a`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S kippo    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/desaster/kippo) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pyasn1, python2-pycryptodome, python2-twistedx (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[kippo]] · [[BlackArch Tools]]

