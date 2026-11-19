---
tool: "flare"
build-system: "scripts"
upstream: "http://www.nowrap.de/flare.html"
recipe: "blackarch"
nix-package: "flare"
aur-package: "flare"
source: "http://www.nowrap.de/flare.html"
license: "custom:unknown"
blackarch-pkgver: "0.6"
---

# Installing flare

> Flare processes an SWF and extracts all scripts from it.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/flare) (version `0.6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S flare    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.nowrap.de/flare.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `lib32-zlib`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[flare]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#flare    # ad-hoc run, no install
nix profile install nixpkgs#flare   # or persist it
```
