---
tool: "wordpot"
build-system: "scripts"
upstream: "https://github.com/gbrindisi/wordpot"
recipe: "blackarch"
source: "https://github.com/gbrindisi/wordpot"
license: "GPL2"
blackarch-pkgver: "44.e96889b"
---

# Installing wordpot

> A Wordpress Honeypot.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wordpot) (version `44.e96889b`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S wordpot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/gbrindisi/wordpot) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-flask (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[wordpot]] · [[BlackArch Tools]]

