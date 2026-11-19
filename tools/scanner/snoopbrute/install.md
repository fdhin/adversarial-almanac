---
tool: "snoopbrute"
build-system: "scripts"
upstream: "https://github.com/m57/snoopbrute"
recipe: "blackarch"
source: "https://github.com/m57/snoopbrute"
license: "GPL2"
blackarch-pkgver: "17.589fbe6"
---

# Installing snoopbrute

> Multithreaded DNS recursive host brute-force tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snoopbrute) (version `17.589fbe6`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S snoopbrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/m57/snoopbrute) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dnspython (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[snoopbrute]] · [[BlackArch Tools]]

