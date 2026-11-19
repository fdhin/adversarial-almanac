---
tool: "kscan"
build-system: "go"
upstream: "https://github.com/lcvvvv/kscan"
recipe: "blackarch"
source: "https://github.com/lcvvvv/kscan"
license: "GPL3"
blackarch-pkgver: "v1.85.r55.gcf76af2"
---

# Installing kscan

> Asset mapping tool that can perform port scanning, TCP fingerprinting and banner capture for specified assets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kscan) (version `v1.85.r55.gcf76af2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S kscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/lcvvvv/kscan/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/lcvvvv/kscan) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

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

Backlinks: [[scanner]] · [[kscan]] · [[BlackArch Tools]]

