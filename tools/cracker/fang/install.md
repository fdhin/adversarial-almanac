---
tool: "fang"
build-system: "scripts"
upstream: "https://github.com/evilsocket/fang"
recipe: "blackarch"
source: "https://github.com/evilsocket/fang"
license: "GPL2"
blackarch-pkgver: "22.4f94552"
---

# Installing fang

> A multi service threaded MD5 cracker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fang) (version `22.4f94552`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S fang    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/evilsocket/fang) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[fang]] · [[BlackArch Tools]]

