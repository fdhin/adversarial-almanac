---
tool: "vane"
build-system: "scripts"
upstream: "https://github.com/delvelabs/vane"
recipe: "blackarch"
aur-package: "vane"
source: "https://github.com/delvelabs/vane"
license: "GPL3"
blackarch-pkgver: "1899.48f9ab5"
---

# Installing vane

> A vulnerability scanner which checks the security of WordPress installations using a black box approach.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vane) (version `1899.48f9ab5`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S vane    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/delvelabs/vane) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[vane]] · [[BlackArch Tools]]

