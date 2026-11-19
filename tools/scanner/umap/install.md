---
tool: "umap"
build-system: "scripts"
upstream: "https://github.com/nccgroup/umap"
recipe: "blackarch"
nix-package: "umap"
source: "https://github.com/nccgroup/umap"
license: "AGPL"
blackarch-pkgver: "25.3ad8121"
---

# Installing umap

> The USB host security assessment tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/umap) (version `25.3ad8121`, license AGPL).

## Arch / BlackArch

```bash
sudo pacman -S umap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/umap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pyserial`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python3`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[umap]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#umap    # ad-hoc run, no install
nix profile install nixpkgs#umap   # or persist it
```
