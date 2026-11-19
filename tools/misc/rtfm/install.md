---
tool: "rtfm"
build-system: "scripts"
upstream: "https://github.com/leostat/rtfm"
recipe: "blackarch"
nix-package: "rtfm"
aur-package: "rtfm"
source: "https://github.com/leostat/rtfm"
license: "custom:unknown"
blackarch-pkgver: "95.a807a80"
---

# Installing rtfm

> A database of common, interesting or useful commands, in one handy referable form.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rtfm) (version `95.a807a80`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rtfm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/leostat/rtfm) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `terminaltables`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[rtfm]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#rtfm    # ad-hoc run, no install
nix profile install nixpkgs#rtfm   # or persist it
```
