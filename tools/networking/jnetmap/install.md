---
tool: "jnetmap"
build-system: "scripts"
upstream: "https://sourceforge.net/projects/jnetmap/files/jNetMap%200.5.5-RC2/"
recipe: "blackarch"
nix-package: "jnetmap"
aur-package: "jnetmap"
source: "https://sourceforge.net/projects/jnetmap/files/jNetMap%200.5.5-RC2/"
license: "GPL3"
blackarch-pkgver: "0.5.5"
---

# Installing jnetmap

> A network monitor of sorts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jnetmap) (version `0.5.5`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S jnetmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://sourceforge.net/projects/jnetmap/files/jNetMap%200.5.5-RC2/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[jnetmap]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#jnetmap    # ad-hoc run, no install
nix profile install nixpkgs#jnetmap   # or persist it
```
