---
tool: "httpx"
build-system: "go"
upstream: "https://github.com/projectdiscovery/httpx"
recipe: "blackarch"
nix-package: "httpx"
aur-package: "httpx"
source: "https://github.com/projectdiscovery/httpx"
license: "MIT"
blackarch-pkgver: "v1.11.0.r59.gbab476e"
---

# Installing httpx

> A fast and multi-purpose HTTP toolkit allow to run multiple probers using retryablehttp library.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httpx) (version `v1.11.0.r59.gbab476e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S httpx    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/httpx/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/httpx) (the recipe uses a versioned archive).

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
    -o $pkgname "./cmd/$pkgname"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[httpx]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#httpx    # ad-hoc run, no install
nix profile install nixpkgs#httpx   # or persist it
```
