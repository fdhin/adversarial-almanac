---
tool: "stowaway"
build-system: "go"
upstream: "https://github.com/ph4ntonn/Stowaway"
recipe: "blackarch"
nix-package: "stowaway"
source: "https://github.com/ph4ntonn/Stowaway"
license: "MIT"
blackarch-pkgver: "v2.2.r43.g7f715be"
---

# Installing stowaway

> A Multi-hop proxy tool for security researchers and pentesters.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stowaway) (version `v2.2.r43.g7f715be`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S stowaway    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/ph4ntonn/Stowaway/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/ph4ntonn/Stowaway) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  for bin in agent admin; do
    GOPATH="$srcdir" go mod download
    GOPATH="$srcdir" go build \
      -trimpath \
      -buildmode=pie \
      -mod=readonly \
      -modcacherw \
      -ldflags "-s -w" \
      -o $pkgname-$bin "$bin/$bin.go"
  done
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[stowaway]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#stowaway    # ad-hoc run, no install
nix profile install nixpkgs#stowaway   # or persist it
```
