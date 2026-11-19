---
tool: "saleae-logic"
build-system: "scripts"
upstream: "https://www.saleae.com/downloads"
recipe: "blackarch"
nix-package: "saleae-logic"
aur-package: "saleae-logic"
source: "https://www.saleae.com/downloads"
license: "custom:unknown"
blackarch-pkgver: "2.3.47"
---

# Installing saleae-logic

> Debug happy.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/saleae-logic) (version `2.3.47`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S saleae-logic    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.saleae.com/downloads) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libxss`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[debugger]] · [[saleae-logic]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#saleae-logic    # ad-hoc run, no install
nix profile install nixpkgs#saleae-logic   # or persist it
```
