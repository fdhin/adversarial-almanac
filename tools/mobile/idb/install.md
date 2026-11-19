---
tool: "idb"
build-system: "ruby"
upstream: "https://rubygems.org/gems/idb"
recipe: "blackarch"
source: "https://rubygems.org/gems/idb"
license: "MIT"
blackarch-pkgver: "2.10.3"
---

# Installing idb

> A tool to simplify some common tasks for iOS pentesting and research.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/idb) (version `2.10.3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S idb    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://rubygems.org/gems/idb) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `qt4`
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[idb]] · [[BlackArch Tools]]

