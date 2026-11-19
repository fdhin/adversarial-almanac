---
tool: "vuls"
build-system: "go"
upstream: "https://github.com/future-architect/vuls"
recipe: "blackarch"
nix-package: "vuls"
source: "https://github.com/future-architect/vuls"
license: "GPL-3.0-only"
blackarch-pkgver: "v0.35.1.r3.g0ba85f8"
---

# Installing vuls

> Vulnerability scanner for Linux/FreeBSD, agentless, written in Go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vuls) (version `v0.35.1.r3.g0ba85f8`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S vuls    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/future-architect/vuls/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/future-architect/vuls) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  VERSION=$(git describe --tags --abbrev=0)
  REVISION=$(git rev-parse --short HEAD)
  BUILDTIME=$(date "+%Y%m%d_%H%M%S")

  GOFLAGS="-trimpath -buildmode=pie -mod=readonly -modcacherw"
  LDFLAGS="-X github.com/future-architect/vuls/config.Version=${VERSION}"
  LDFLAGS="$LDFLAGS -X github.com/future-architect/vuls/config.Revision=build-${BUILDTIME}_${REVISION}"

  GOPATH="$srcdir" go mod download

  GOPATH="$srcdir" go build \
    $GOFLAGS \
    -ldflags "$LDFLAGS" \
    -o $pkgname "./cmd/$pkgname"

  GOPATH="$srcdir" go build \
    $GOFLAGS \
    -ldflags "$LDFLAGS" \
    -o "$pkgname-scanner" ./cmd/scanner
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[vuls]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#vuls    # ad-hoc run, no install
nix profile install nixpkgs#vuls   # or persist it
```
