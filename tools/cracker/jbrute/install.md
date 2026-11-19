---
tool: "jbrute"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/jbrute/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/jbrute/"
license: "GPL3"
blackarch-pkgver: "0.99"
---

# Installing jbrute

> Open Source Security tool to audit hashed passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jbrute) (version `0.99`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S jbrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/jbrute/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/jbrute`

Backlinks: [[cracker]] · [[jbrute]] · [[BlackArch Tools]]

