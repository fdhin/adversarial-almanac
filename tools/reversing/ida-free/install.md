---
tool: "ida-free"
build-system: "scripts"
upstream: "https://hex-rays.com/ida-free/"
recipe: "blackarch"
nix-package: "ida-free"
aur-package: "ida-free"
source: "https://hex-rays.com/ida-free/"
license: "custom:unknown"
blackarch-pkgver: "8.4.240527"
provides: ["ida64"]
---

# Installing ida-free

> Freeware version of the world's smartest and most feature-full disassembler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ida-free) (version `8.4.240527`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ida-free    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://hex-rays.com/ida-free/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `fakechroot`

## Installed layout (per recipe)

- Commands: `/usr/bin/ida64`
- Data: `/usr/share/applications`, `/usr/share/icons`

Backlinks: [[reversing]] · [[ida-free]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ida-free    # ad-hoc run, no install
nix profile install nixpkgs#ida-free   # or persist it
```
