---
tool: "vulnx"
build-system: "scripts"
upstream: "https://github.com/anouarbensaad/vulnx"
recipe: "blackarch"
nix-package: "vulnx"
aur-package: "vulnx"
source: "https://github.com/anouarbensaad/vulnx"
license: "MIT"
blackarch-pkgver: "v1.9.r47.gbcf451d"
---

# Installing vulnx

> Cms and vulnerabilites detector & An intelligent bot auto shell injector.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vulnx) (version `v1.9.r47.gbcf451d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S vulnx    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/anouarbensaad/vulnx) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[vulnx]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#vulnx    # ad-hoc run, no install
nix profile install nixpkgs#vulnx   # or persist it
```
