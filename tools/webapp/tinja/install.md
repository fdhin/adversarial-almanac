---
tool: "tinja"
build-system: "go"
upstream: "https://github.com/Hackmanit/TInjA"
recipe: "blackarch"
source: "https://github.com/Hackmanit/TInjA"
license: "Apache-2.0"
blackarch-pkgver: "1.2.0.r5.g507d903"
---

# Installing tinja

> CLI tool for testing web pages for template injection vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tinja) (version `1.2.0.r5.g507d903`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S tinja    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Hackmanit/TInjA/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/Hackmanit/TInjA) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $_pkgname

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

Backlinks: [[webapp]] · [[tinja]] · [[BlackArch Tools]]

