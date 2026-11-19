---
tool: "pasv-agrsv"
build-system: "scripts"
upstream: "https://github.com/isaudits/pasv-agrsv"
recipe: "blackarch"
source: "https://github.com/isaudits/pasv-agrsv"
license: "GPL3"
blackarch-pkgver: "57.6bb54f7"
---

# Installing pasv-agrsv

> Passive recon / OSINT automation script.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pasv-agrsv) (version `57.6bb54f7`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pasv-agrsv    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/isaudits/pasv-agrsv) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dnspython, python2-elixir, python2-sqlalchemy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[pasv-agrsv]] · [[BlackArch Tools]]

