---
tool: "naft"
build-system: "scripts"
upstream: "https://blog.didierstevens.com/my-software/#NAFT"
recipe: "blackarch"
source: "https://blog.didierstevens.com/my-software/#NAFT"
license: "custom:unknown"
blackarch-pkgver: "0.0.9"
---

# Installing naft

> Network Appliance Forensic Toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/naft) (version `0.0.9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S naft    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://blog.didierstevens.com/my-software/#NAFT) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-yara (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[naft]] · [[BlackArch Tools]]

