---
tool: "onionscan"
build-system: "go"
upstream: "https://github.com/s-rah/onionscan"
recipe: "blackarch"
source: "https://github.com/s-rah/onionscan"
license: "MIT"
blackarch-pkgver: "130.da42865"
---

# Installing onionscan

> Scan Onion Services for Security Issues.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/onionscan) (version `130.da42865`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S onionscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/s-rah/onionscan/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/s-rah/onionscan) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `libexif`

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
    -o $pkgname-bin .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[onionscan]] · [[BlackArch Tools]]

