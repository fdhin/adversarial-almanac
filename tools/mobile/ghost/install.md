---
tool: "ghost"
build-system: "scripts"
upstream: "https://github.com/EntySec/Ghost"
recipe: "blackarch"
nix-package: "ghost"
source: "https://github.com/EntySec/Ghost"
license: "MIT"
blackarch-pkgver: "426.bf38c7e"
---

# Installing ghost

> Android post-exploitation framework that exploits the Android Debug Bridge to remotely access an Android device.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ghost) (version `426.bf38c7e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ghost    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/EntySec/Ghost) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[ghost]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ghost    # ad-hoc run, no install
nix profile install nixpkgs#ghost   # or persist it
```
