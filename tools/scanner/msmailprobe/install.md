---
tool: "msmailprobe"
build-system: "go"
upstream: "https://github.com/busterb/msmailprobe"
recipe: "blackarch"
source: "https://github.com/busterb/msmailprobe"
license: "MIT"
blackarch-pkgver: "1.c01c8bf"
---

# Installing msmailprobe

> Office 365 and Exchange Enumeration tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/msmailprobe) (version `1.c01c8bf`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S msmailprobe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/busterb/msmailprobe/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/busterb/msmailprobe) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[msmailprobe]] · [[BlackArch Tools]]

