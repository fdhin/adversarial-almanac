---
tool: "smod"
build-system: "scripts"
upstream: "https://github.com/enddo/smod"
recipe: "blackarch"
source: "https://github.com/enddo/smod"
license: "GPL2"
blackarch-pkgver: "53.7eb8423"
---

# Installing smod

> A modular framework with every kind of diagnostic and offensive feature you could need in order to pentest modbus protocol.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smod) (version `53.7eb8423`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S smod    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/enddo/smod) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-archinfo, python2-asn1crypto, python2-pexpect, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[smod]] · [[BlackArch Tools]]

