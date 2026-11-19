---
tool: "lunar"
build-system: "scripts"
upstream: "https://github.com/lateralblast/lunar"
recipe: "blackarch"
nix-package: "lunar"
aur-package: "lunar"
source: "https://github.com/lateralblast/lunar"
license: "custom:unknown"
blackarch-pkgver: "1386.c1e9ed23"
---

# Installing lunar

> A UNIX security auditing tool based on several security frameworks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lunar) (version `1386.c1e9ed23`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S lunar    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/lateralblast/lunar) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bc`
  - `netkit-bsd-finger`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[lunar]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#lunar    # ad-hoc run, no install
nix profile install nixpkgs#lunar   # or persist it
```
