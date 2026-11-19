---
tool: "dnsx"
build-system: "go"
upstream: "https://github.com/projectdiscovery/dnsx"
recipe: "blackarch"
nix-package: "dnsx"
aur-package: "dnsx"
source: "https://github.com/projectdiscovery/dnsx"
license: "MIT"
blackarch-pkgver: "v1.2.1.r493.gc72b42d"
---

# Installing dnsx

> Fast and multi-purpose DNS toolkit allow to run multiple DNS queries of your choice with a list of user-supplied resolvers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsx) (version `v1.2.1.r493.gc72b42d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dnsx    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/dnsx/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/dnsx) (the recipe uses a versioned archive).

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
    -ldflags "-s -w" \
    -o $pkgname ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[dnsx]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dnsx    # ad-hoc run, no install
nix profile install nixpkgs#dnsx   # or persist it
```
