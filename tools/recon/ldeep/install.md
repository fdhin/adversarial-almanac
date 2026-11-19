---
tool: "ldeep"
build-system: "scripts"
upstream: "https://github.com/franc-pentest/ldeep"
recipe: "blackarch"
nix-package: "ldeep"
source: "https://github.com/franc-pentest/ldeep"
license: "MIT"
blackarch-pkgver: "2.0.3.r15.gb57f12e"
---

# Installing ldeep

> In-depth ldap enumeration utility.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ldeep) (version `2.0.3.r15.gb57f12e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ldeep    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/franc-pentest/ldeep) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[ldeep]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ldeep    # ad-hoc run, no install
nix profile install nixpkgs#ldeep   # or persist it
```
