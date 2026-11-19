---
tool: "exe2hex"
build-system: "scripts"
upstream: "https://github.com/g0tmi1k/exe2hex"
recipe: "blackarch"
nix-package: "exe2hex"
source: "https://github.com/g0tmi1k/exe2hex"
license: "MIT"
blackarch-pkgver: "1.5.1.r6.ge563b35"
---

# Installing exe2hex

> Inline file transfer using in-built Windows tools (DEBUG.exe or PowerShell).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/exe2hex) (version `1.5.1.r6.ge563b35`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S exe2hex    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/g0tmi1k/exe2hex) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[disassembler]] · [[exe2hex]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#exe2hex    # ad-hoc run, no install
nix profile install nixpkgs#exe2hex   # or persist it
```
