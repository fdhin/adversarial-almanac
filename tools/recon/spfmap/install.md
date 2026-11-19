---
tool: "spfmap"
build-system: "go"
upstream: "https://github.com/BishopFox/spfmap"
recipe: "blackarch"
source: "https://github.com/BishopFox/spfmap"
license: "custom:unknown"
blackarch-pkgver: "8.a42d15a"
---

# Installing spfmap

> A program to map out SPF and DKIM records for a large number of domains.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spfmap) (version `8.a42d15a`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S spfmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/BishopFox/spfmap/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/BishopFox/spfmap) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[spfmap]] · [[BlackArch Tools]]

