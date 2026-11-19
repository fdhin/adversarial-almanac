---
tool: "procyon"
build-system: "scripts"
upstream: "https://github.com/mstrobel/procyon"
recipe: "blackarch"
nix-package: "procyon"
source: "https://github.com/mstrobel/procyon"
license: "custom:unknown"
blackarch-pkgver: "0.6"
---

# Installing procyon

> A suite of Java metaprogramming tools focused on code generation and analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/procyon) (version `0.6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S procyon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mstrobel/procyon) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[procyon]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#procyon    # ad-hoc run, no install
nix profile install nixpkgs#procyon   # or persist it
```
