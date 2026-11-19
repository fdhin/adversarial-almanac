---
tool: "pass-station"
build-system: "ruby"
upstream: "https://noraj.github.io/pass-station/"
recipe: "blackarch"
source: "https://noraj.github.io/pass-station/"
license: "MIT"
blackarch-pkgver: "v3.0.0.r35.g3b196ae"
---

# Installing pass-station

> CLI & library to search for default credentials among thousands of Products / Vendors.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pass-station) (version `v3.0.0.r35.g3b196ae`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pass-station    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://noraj.github.io/pass-station/) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-docopt`
  - `ruby-paint`

## Exact build steps from the recipe

```bash
cd $pkgname

  gem build "$pkgname.gemspec"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[pass-station]] · [[BlackArch Tools]]

