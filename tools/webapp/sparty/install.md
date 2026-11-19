---
tool: "sparty"
build-system: "scripts"
upstream: "http://sparty.secniche.org/"
recipe: "blackarch"
source: "http://sparty.secniche.org/"
license: "BSD"
blackarch-pkgver: "0.1"
---

# Installing sparty

> An open source tool written in python to audit web applications using sharepoint and frontpage architecture.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sparty) (version `0.1`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S sparty    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sparty.secniche.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[sparty]] · [[BlackArch Tools]]

