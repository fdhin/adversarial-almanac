---
tool: "cansina"
build-system: "scripts"
upstream: "https://github.com/deibit/cansina"
recipe: "blackarch"
nix-package: "cansina"
source: "https://github.com/deibit/cansina"
license: "GPL3"
blackarch-pkgver: "65.ee7806a"
---

# Installing cansina

> A python-based Web Content Discovery Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cansina) (version `65.ee7806a`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cansina    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/deibit/cansina) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[cansina]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cansina    # ad-hoc run, no install
nix profile install nixpkgs#cansina   # or persist it
```
