---
tool: "creddump"
build-system: "scripts"
upstream: "https://github.com/moyix/creddump"
recipe: "blackarch"
source: "https://github.com/moyix/creddump"
license: "GPL2"
blackarch-pkgver: "3.ed95e1a"
provides: ["cachedump", "lsadump", "pwdump"]
---

# Installing creddump

> A python tool to extract various credentials and secrets from Windows registry hives.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/creddump) (version `3.ed95e1a`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S creddump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/moyix/creddump) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pycryptodome (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/cachedump`, `/usr/bin/lsadump`, `/usr/bin/pwdump`
- Data: `/usr/share/windows`

Backlinks: [[cracker]] · [[creddump]] · [[BlackArch Tools]]

