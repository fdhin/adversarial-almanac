---
tool: "rbkb"
build-system: "ruby"
upstream: "https://github.com/emonti/rbkb"
recipe: "blackarch"
source: "https://github.com/emonti/rbkb"
license: "MIT"
blackarch-pkgver: "v0.7.3.r3.g4645025"
---

# Installing rbkb

> A miscellaneous collection of command-line tools related to pen-testing and reversing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rbkb) (version `v0.7.3.r3.g4645025`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S rbkb    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://github.com/emonti/rbkb) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Exact build steps from the recipe

```bash
cd $pkgname

  gem build rbkb.gemspec
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[rbkb]] · [[BlackArch Tools]]

