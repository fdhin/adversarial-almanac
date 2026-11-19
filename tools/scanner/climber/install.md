---
tool: "climber"
build-system: "scripts"
upstream: "https://github.com/raffaele-forte/climber"
recipe: "blackarch"
source: "https://github.com/raffaele-forte/climber"
license: "GPL3"
blackarch-pkgver: "30.5530a78"
---

# Installing climber

> Check UNIX/Linux systems for privilege escalation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/climber) (version `30.5530a78`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S climber    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/raffaele-forte/climber) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-exscript, python2-mako, python2-paramiko, python2-pycryptodome (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[climber]] · [[BlackArch Tools]]

