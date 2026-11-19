---
tool: "guymager"
build-system: "scripts"
upstream: "http://guymager.sourceforge.net/"
recipe: "blackarch"
nix-package: "guymager"
aur-package: "guymager"
source: "http://guymager.sourceforge.net/"
license: "GPL"
blackarch-pkgver: "0.8.13"
kali-package: "guymager"
kali-version: "0.8.13-3"
---

# Installing guymager

> A forensic imager for media acquisition.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/guymager) (version `0.8.13`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S guymager    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install guymager    # Kali package version 0.8.13-3
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://guymager.sourceforge.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `qt5-base`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[guymager]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#guymager    # ad-hoc run, no install
nix profile install nixpkgs#guymager   # or persist it
```
