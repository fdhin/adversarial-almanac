---
tool: "mallory"
build-system: "go"
upstream: "https://github.com/justmao945/mallory"
recipe: "blackarch"
source: "https://github.com/justmao945/mallory"
license: "MIT"
blackarch-pkgver: "142.acabe6b"
---

# Installing mallory

> HTTP/HTTPS proxy over SSH.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mallory) (version `142.acabe6b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S mallory    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/justmao945/mallory/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/justmao945/mallory) (the recipe uses a versioned archive).

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
    -o $pkgname ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[mallory]] · [[BlackArch Tools]]

