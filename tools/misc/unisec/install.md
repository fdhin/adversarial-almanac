---
tool: "unisec"
build-system: "ruby"
upstream: "https://noraj.github.io/unisec"
recipe: "blackarch"
aur-package: "unisec"
source: "https://noraj.github.io/unisec"
license: "MIT"
blackarch-pkgver: "0.0.10"
---

# Installing unisec

> Unicode Security Toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/unisec) (version `0.0.10`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S unisec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://noraj.github.io/unisec) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[unisec]] · [[BlackArch Tools]]

