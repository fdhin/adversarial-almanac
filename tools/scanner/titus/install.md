---
tool: "titus"
build-system: "go"
upstream: "https://github.com/praetorian-inc/titus"
recipe: "blackarch"
source: "https://github.com/praetorian-inc/titus"
license: "Apache-2.0"
blackarch-pkgver: "v1.2.11.r15.gbfc5a99"
---

# Installing titus

> High-performance secrets scanner based on NoseyParker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/titus) (version `v1.2.11.r15.gbfc5a99`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S titus    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/praetorian-inc/titus/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/praetorian-inc/titus) (the recipe uses a versioned archive).

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
  GOPATH="$srcdir" GOWORK=off CGO_ENABLED=0 go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[titus]] · [[BlackArch Tools]]

