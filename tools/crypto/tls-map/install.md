---
tool: "tls-map"
build-system: "ruby"
upstream: "https://noraj.github.io/tls-map/"
recipe: "blackarch"
source: "https://noraj.github.io/tls-map/"
license: "MIT"
blackarch-pkgver: "v3.4.0.r3.gd5c7465"
---

# Installing tls-map

> CLI & library for TLS cipher suites manipulation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tls-map) (version `v3.4.0.r3.gd5c7465`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S tls-map    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://noraj.github.io/tls-map/) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-csv`
  - `ruby-default-gems`
  - `ruby-docopt`
  - `ruby-paint`
  - `ruby-rexml`
  - `rubygems`

## Exact build steps from the recipe

```bash
cd $pkgname

  gem build "$pkgname.gemspec"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[tls-map]] · [[BlackArch Tools]]

