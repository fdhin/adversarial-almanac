---
tool: "malmon"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/malmon/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/malmon/"
license: "custom:unknown"
blackarch-pkgver: "0.3"
provides: ["malmon-scan"]
---

# Installing malmon

> Hosting exploit/backdoor detection daemon.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/malmon) (version `0.3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S malmon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/malmon/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pyinotify (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/malmon-scan`

Backlinks: [[defensive]] · [[malmon]] · [[BlackArch Tools]]

