---
tool: "sakis3g"
build-system: "scripts"
upstream: "http://www.sakis3g.org/"
recipe: "blackarch"
source: "http://www.sakis3g.org/"
license: "GPL2"
blackarch-pkgver: "0.2.0e"
kali-package: "sakis3g"
kali-version: "0.2.0e+git20150717-0kali2"
---

# Installing sakis3g

> An all-in-one script for connecting with 3G.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sakis3g) (version `0.2.0e`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S sakis3g    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sakis3g    # Kali package version 0.2.0e+git20150717-0kali2
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.sakis3g.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `net-tools`
  - `ppp`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[sakis3g]] · [[BlackArch Tools]]

