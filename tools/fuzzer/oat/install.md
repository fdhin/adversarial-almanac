---
tool: "oat"
build-system: "scripts"
upstream: "http://www.cqure.net/wp/test/"
recipe: "blackarch"
source: "http://www.cqure.net/wp/test/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1.3.1"
---

# Installing oat

> A toolkit that could be used to audit security within Oracle database servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/oat) (version `1.3.1`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S oat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.cqure.net/wp/test/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[oat]] · [[BlackArch Tools]]

