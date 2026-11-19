---
tool: "dtp-spoof"
build-system: "scripts"
upstream: "https://github.com/fleetcaptain/dtp-spoof"
recipe: "blackarch"
source: "https://github.com/fleetcaptain/dtp-spoof"
license: "custom:unknown"
blackarch-pkgver: "5.3ae05fc"
---

# Installing dtp-spoof

> Python script/security tool to test Dynamic Trunking Protocol configuration on a switch.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dtp-spoof) (version `5.3ae05fc`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dtp-spoof    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/fleetcaptain/dtp-spoof) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[dtp-spoof]] · [[BlackArch Tools]]

