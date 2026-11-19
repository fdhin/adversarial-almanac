---
tool: "brakeman"
build-system: "scripts"
upstream: "https://brakemanscanner.org/"
recipe: "blackarch"
nix-package: "brakeman"
source: "https://brakemanscanner.org/"
license: "MIT"
blackarch-pkgver: "v8.1.0.r2.g4bfdad0c3"
---

# Installing brakeman

> A static analysis security vulnerability scanner for Ruby on Rails applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/brakeman) (version `v8.1.0.r2.g4bfdad0c3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S brakeman    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://brakemanscanner.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[brakeman]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#brakeman    # ad-hoc run, no install
nix profile install nixpkgs#brakeman   # or persist it
```
