---
tool: "boofuzz"
build-system: "scripts"
upstream: "https://github.com/jtpereyda/boofuzz"
recipe: "blackarch"
nix-package: "boofuzz"
source: "https://github.com/jtpereyda/boofuzz"
license: "GPL-2.0-only"
blackarch-pkgver: "v0.4.2.r44.g41a808e"
---

# Installing boofuzz

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/boofuzz) (version `v0.4.2.r44.g41a808e`, license GPL-2.0-only).

## Arch / BlackArch

```bash
sudo pacman -S boofuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/jtpereyda/boofuzz) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[boofuzz]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#boofuzz    # ad-hoc run, no install
nix profile install nixpkgs#boofuzz   # or persist it
```
