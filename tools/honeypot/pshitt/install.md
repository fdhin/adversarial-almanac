---
tool: "pshitt"
build-system: "scripts"
upstream: "https://github.com/regit/pshitt"
recipe: "blackarch"
source: "https://github.com/regit/pshitt"
license: "GPL3"
blackarch-pkgver: "23.dae7931"
---

# Installing pshitt

> A lightweight fake SSH server designed to collect authentication data sent by intruders.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pshitt) (version `23.dae7931`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pshitt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/regit/pshitt) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-daemon, python2-paramiko (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[pshitt]] · [[BlackArch Tools]]

