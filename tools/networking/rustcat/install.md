---
tool: "rustcat"
build-system: "go"
upstream: "https://github.com/robiot/rustcat"
recipe: "blackarch"
nix-package: "rustcat"
aur-package: "rustcat"
source: "https://github.com/robiot/rustcat"
license: "MIT"
blackarch-pkgver: "v3.0.0.r4.g245c791"
---

# Installing rustcat

> A modern port listener and reverse shell.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rustcat) (version `v3.0.0.r4.g245c791`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S rustcat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/robiot/rustcat/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/robiot/rustcat) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  cargo build --release
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[rustcat]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#rustcat    # ad-hoc run, no install
nix profile install nixpkgs#rustcat   # or persist it
```
