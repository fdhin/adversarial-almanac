---
tool: "zdns"
build-system: "go"
upstream: "https://github.com/zmap/zdns"
recipe: "blackarch"
nix-package: "zdns"
source: "https://github.com/zmap/zdns"
license: "Apache"
blackarch-pkgver: "v2.1.1.r20.gdf6af70"
---

# Installing zdns

> Fast CLI DNS Lookup Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zdns) (version `v2.1.1.r20.gdf6af70`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S zdns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/zmap/zdns/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/zmap/zdns) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  make $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[zdns]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#zdns    # ad-hoc run, no install
nix profile install nixpkgs#zdns   # or persist it
```
