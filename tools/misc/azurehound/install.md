---
tool: "azurehound"
build-system: "go"
upstream: "https://github.com/SpecterOps/AzureHound"
recipe: "blackarch"
nix-package: "azurehound"
source: "https://github.com/SpecterOps/AzureHound"
license: "GPL-3.0-only"
blackarch-pkgver: "3.1.3.r0.gb02d457"
---

# Installing azurehound

> Azure data exporter for BloodHound.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/azurehound) (version `3.1.3.r0.gb02d457`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S azurehound    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/SpecterOps/AzureHound/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/SpecterOps/AzureHound) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w -X github.com/bloodhoundad/azurehound/v2/constants.Version=`git describe tags --exact-match 2> /dev/null || git rev-parse HEAD`" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[azurehound]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#azurehound    # ad-hoc run, no install
nix profile install nixpkgs#azurehound   # or persist it
```
