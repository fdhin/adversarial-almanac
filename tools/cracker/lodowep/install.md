---
tool: "lodowep"
build-system: "scripts"
upstream: "http://www.cqure.net/wp/lodowep/"
recipe: "blackarch"
source: "http://www.cqure.net/wp/lodowep/"
license: "GPL"
blackarch-pkgver: "1.2.1"
---

# Installing lodowep

> Lodowep is a tool for analyzing password strength of accounts on a Lotus Domino webserver system.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lodowep) (version `1.2.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S lodowep    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.cqure.net/wp/lodowep/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[lodowep]] · [[BlackArch Tools]]

