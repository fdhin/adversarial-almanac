---
tool: "cloudlist"
build-system: "go"
upstream: "https://github.com/projectdiscovery/cloudlist"
recipe: "blackarch"
nix-package: "cloudlist"
aur-package: "cloudlist"
source: "https://github.com/projectdiscovery/cloudlist"
license: "MIT"
blackarch-pkgver: "1017.b4c3216"
---

# Installing cloudlist

> A tool for listing Assets from multiple Cloud Providers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cloudlist) (version `1017.b4c3216`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cloudlist    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/cloudlist/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/cloudlist) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

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

Backlinks: [[recon]] · [[cloudlist]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cloudlist    # ad-hoc run, no install
nix profile install nixpkgs#cloudlist   # or persist it
```
