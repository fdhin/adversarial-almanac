---
tool: "ad-miner"
build-system: "scripts"
upstream: "https://github.com/Mazars-Tech/AD_Miner"
recipe: "blackarch"
nix-package: "ad-miner"
source: "https://github.com/Mazars-Tech/AD_Miner"
license: "GPL3"
blackarch-pkgver: "v1.9.0.r0.gac3d473"
---

# Installing ad-miner

> Active Directory audit tool that extract data from Bloodhound to uncover security weaknesses and generate an HTML report

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ad-miner) (version `v1.9.0.r0.gac3d473`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ad-miner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Mazars-Tech/AD_Miner) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `poetry`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ad-miner]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ad-miner    # ad-hoc run, no install
nix profile install nixpkgs#ad-miner   # or persist it
```
