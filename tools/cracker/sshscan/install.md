---
tool: "sshscan"
build-system: "scripts"
upstream: "https://github.com/getdual/scripts-n-tools/blob/master/sshscan.py"
recipe: "blackarch"
source: "https://github.com/getdual/scripts-n-tools/blob/master/sshscan.py"
license: "unknown"
blackarch-pkgver: "1.0"
---

# Installing sshscan

> A horizontal SSH scanner that scans large swaths of IPv4 space for a single SSH user and pass.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sshscan) (version `1.0`, license unknown).

## Arch / BlackArch

```bash
sudo pacman -S sshscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/getdual/scripts-n-tools/blob/master/sshscan.py) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-netaddr, python2-pexpect (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[sshscan]] · [[BlackArch Tools]]

