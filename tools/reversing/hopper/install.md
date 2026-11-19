---
tool: "hopper"
build-system: "scripts"
upstream: "https://www.hopperapp.com/download.html?"
recipe: "blackarch"
nix-package: "hopper"
source: "https://www.hopperapp.com/download.html?"
license: "custom:unknown"
blackarch-pkgver: "6.6.0"
---

# Installing hopper

> Reverse engineering tool that lets you disassemble, decompile and debug your applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hopper) (version `6.6.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hopper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.hopperapp.com/download.html?) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `hicolor-icon-theme` — hicolor-icon-theme (any distro)
  - `libbsd`
  - `python2`
  - `qt5-base`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`

Backlinks: [[reversing]] · [[hopper]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#hopper    # ad-hoc run, no install
nix profile install nixpkgs#hopper   # or persist it
```
