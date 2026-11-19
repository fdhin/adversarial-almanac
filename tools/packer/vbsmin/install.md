---
tool: "vbsmin"
build-system: "ruby"
upstream: "https://noraj.github.io/vbsmin/"
recipe: "blackarch"
source: "https://noraj.github.io/vbsmin/"
license: "MIT"
blackarch-pkgver: "v1.2.0.r34.gf907829"
---

# Installing vbsmin

> VBScript minifier.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vbsmin) (version `v1.2.0.r34.gf907829`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S vbsmin    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://noraj.github.io/vbsmin/) (the recipe uses a versioned archive).

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

Backlinks: [[packer]] · [[vbsmin]] · [[BlackArch Tools]]

