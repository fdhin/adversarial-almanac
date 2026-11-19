---
tool: "drupalscan"
build-system: "ruby"
upstream: "https://rubygems.org/gems/DrupalScan/"
recipe: "blackarch"
source: "https://rubygems.org/gems/DrupalScan/"
license: "custom:unknown"
blackarch-pkgver: "0.5.2"
provides: ["DrupalScan"]
---

# Installing drupalscan

> Simple non-intrusive Drupal scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/drupalscan) (version `0.5.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S drupalscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://rubygems.org/gems/DrupalScan/) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- Commands: `/usr/bin/DrupalScan`

Backlinks: [[webapp]] · [[drupalscan]] · [[BlackArch Tools]]

