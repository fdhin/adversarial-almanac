---
tool: "rootbrute"
build-system: "scripts"
upstream: "http://www.packetstormsecurity.org/"
recipe: "blackarch"
source: "http://www.packetstormsecurity.org/"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing rootbrute

> Local root account bruteforcer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rootbrute) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rootbrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.packetstormsecurity.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pexpect (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[rootbrute]] · [[BlackArch Tools]]

