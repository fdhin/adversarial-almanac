---
tool: "haiti"
build-system: "ruby"
upstream: "https://noraj.github.io/haiti/"
recipe: "blackarch"
nix-package: "haiti"
aur-package: "haiti"
source: "https://noraj.github.io/haiti/"
license: "MIT"
blackarch-pkgver: "4.0.0.r49.g2b06f11"
---

# Installing haiti

> Hash type identifier (CLI & lib).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/haiti) (version `4.0.0.r49.g2b06f11`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S haiti    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://noraj.github.io/haiti/) (the recipe uses a versioned archive).

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
- Data: `/usr/share/man`

Backlinks: [[crypto]] · [[haiti]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#haiti    # ad-hoc run, no install
nix profile install nixpkgs#haiti   # or persist it
```
