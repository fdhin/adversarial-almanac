---
tool: "metacoretex"
build-system: "scripts"
upstream: "http://metacoretex.sourceforge.net/"
recipe: "blackarch"
source: "http://metacoretex.sourceforge.net/"
license: "custom"
blackarch-pkgver: "0.8.0"
---

# Installing metacoretex

> MetaCoretex is an entirely JAVA vulnerability scanning framework for databases.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/metacoretex) (version `0.8.0`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S metacoretex    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://metacoretex.sourceforge.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[database]] · [[metacoretex]] · [[BlackArch Tools]]

