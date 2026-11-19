---
tool: "obfs4proxy"
build-system: "go"
upstream: "https://gitlab.com/yawning/obfs4"
recipe: "blackarch"
aur-package: "obfs4proxy"
source: "https://gitlab.com/yawning/obfs4"
license: "BSD"
blackarch-pkgver: "0.0.13"
---

# Installing obfs4proxy

> A pluggable transport proxy written in Go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/obfs4proxy) (version `0.0.13`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S obfs4proxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://gitlab.com/yawning/obfs4/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://gitlab.com/yawning/obfs4) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd "obfs4-$pkgname-$pkgver"

  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname-bin ./$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[proxy]] · [[obfs4proxy]] · [[BlackArch Tools]]

