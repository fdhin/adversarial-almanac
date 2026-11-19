---
tool: "hakrevdns"
build-system: "go"
upstream: "https://github.com/hakluke/hakrevdns"
recipe: "blackarch"
source: "https://github.com/hakluke/hakrevdns"
license: "MIT"
blackarch-pkgver: "49.2a847d3"
---

# Installing hakrevdns

> Small, fast tool for performing reverse DNS lookups en masse.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hakrevdns) (version `49.2a847d3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S hakrevdns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/hakluke/hakrevdns/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/hakluke/hakrevdns) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod init go.mod
  GOPATH="$srcdir" go mod tidy
  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[hakrevdns]] · [[BlackArch Tools]]

