---
tool: "second-order"
build-system: "go"
upstream: "https://github.com/mhmdiaa/second-order"
recipe: "blackarch"
source: "https://github.com/mhmdiaa/second-order"
license: "MIT"
blackarch-pkgver: "v3.2.r12.g400a81d"
---

# Installing second-order

> Second-order subdomain takeover scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/second-order) (version `v3.2.r12.g400a81d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S second-order    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/mhmdiaa/second-order/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/mhmdiaa/second-order) (the recipe uses a versioned archive).

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
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[second-order]] · [[BlackArch Tools]]

