---
tool: "rabid"
build-system: "ruby"
upstream: "https://noraj.github.io/rabid/"
recipe: "blackarch"
aur-package: "rabid"
source: "https://noraj.github.io/rabid/"
license: "MIT"
blackarch-pkgver: "v0.3.0.r35.gb242107"
---

# Installing rabid

> A CLI tool and library allowing to simply decode all kind of BigIP cookies.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rabid) (version `v0.3.0.r35.gb242107`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S rabid    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://noraj.github.io/rabid/) (the recipe uses a versioned archive).

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

  gem build bigipcookie.gemspec
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[rabid]] · [[BlackArch Tools]]

