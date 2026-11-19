---
tool: "tcpcat"
build-system: "go"
upstream: "https://github.com/NycolazSec/tcpcat"
recipe: "blackarch"
source: "https://github.com/NycolazSec/tcpcat"
license: "Apache-2.0"
blackarch-pkgver: "v1.5.0.r42.g739fa9a"
---

# Installing tcpcat

> High-performance network reconnaissance and vulnerability intelligence scanner with an eBPF/AF_XDP engine.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tcpcat) (version `v1.5.0.r42.g739fa9a`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S tcpcat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/NycolazSec/tcpcat/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/NycolazSec/tcpcat) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `iputils`

## Exact build steps from the recipe

```bash
cd $pkgname

  export CGO_ENABLED=0
  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o "$pkgname" ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[tcpcat]] · [[BlackArch Tools]]

