---
tool: "graphw00f"
build-system: "scripts"
upstream: "https://github.com/dolevf/graphw00f"
recipe: "blackarch"
nix-package: "graphw00f"
source: "https://github.com/dolevf/graphw00f"
license: "BSD"
blackarch-pkgver: "1.2.1.r4.g4901f82"
---

# Installing graphw00f

> GraphQL endpoint detection and engine fingerprinting.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/graphw00f) (version `1.2.1.r4.g4901f82`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S graphw00f    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/dolevf/graphw00f) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[graphw00f]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#graphw00f    # ad-hoc run, no install
nix profile install nixpkgs#graphw00f   # or persist it
```
