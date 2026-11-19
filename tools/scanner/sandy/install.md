---
tool: "sandy"
build-system: "scripts"
upstream: "https://github.com/donctl/sandy"
recipe: "blackarch"
source: "https://github.com/donctl/sandy"
license: "GPL2"
blackarch-pkgver: "6.531ab16"
---

# Installing sandy

> An open-source Samsung phone encryption assessment framework

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sandy) (version `6.531ab16`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S sandy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/donctl/sandy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pbkdf2, python2-pexpect, python2-pycryptodome (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[sandy]] · [[BlackArch Tools]]

