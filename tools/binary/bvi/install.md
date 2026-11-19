---
tool: "bvi"
build-system: "scripts"
upstream: "http://bvi.sourceforge.net/"
recipe: "blackarch"
nix-package: "bvi"
aur-package: "bvi"
source: "http://bvi.sourceforge.net/"
license: "GPL"
blackarch-pkgver: "1.4.2"
---

# Installing bvi

> A display-oriented editor for binary files operate like "vi" editor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bvi) (version `1.4.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S bvi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://bvi.sourceforge.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ncurses5-compat-libs`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[bvi]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#bvi    # ad-hoc run, no install
nix profile install nixpkgs#bvi   # or persist it
```
