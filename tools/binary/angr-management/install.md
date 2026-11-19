---
tool: "angr-management"
build-system: "scripts"
upstream: "https://github.com/angr/angr-management"
recipe: "blackarch"
nix-package: "angr-management"
source: "https://github.com/angr/angr-management"
license: "BSD-2-Clause"
blackarch-pkgver: "9.2.179"
---

# Installing angr-management

> The official angr GUI.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/angr-management) (version `9.2.179`, license BSD-2-Clause).

## Arch / BlackArch

```bash
sudo pacman -S angr-management    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/angr/angr-management) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`

Backlinks: [[binary]] · [[angr-management]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#angr-management    # ad-hoc run, no install
nix profile install nixpkgs#angr-management   # or persist it
```
