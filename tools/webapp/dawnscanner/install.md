---
tool: "dawnscanner"
build-system: "scripts"
upstream: "https://github.com/thesp0nge/dawnscanner"
recipe: "blackarch"
source: "https://github.com/thesp0nge/dawnscanner"
license: "MIT"
blackarch-pkgver: "v2.2.0.r15.g0d647fc"
---

# Installing dawnscanner

> A static analysis security scanner for ruby written web applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dawnscanner) (version `v2.2.0.r15.g0d647fc`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dawnscanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/thesp0nge/dawnscanner) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[dawnscanner]] · [[BlackArch Tools]]

