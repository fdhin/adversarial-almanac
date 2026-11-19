---
tool: "mylg"
build-system: "go"
upstream: "https://github.com/mehrdadrad/mylg"
recipe: "blackarch"
source: "https://github.com/mehrdadrad/mylg"
license: "MIT"
blackarch-pkgver: "659.faba867"
---

# Installing mylg

> Network Diagnostic Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mylg) (version `659.faba867`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S mylg    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/mehrdadrad/mylg/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/mehrdadrad/mylg) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

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

Backlinks: [[networking]] · [[mylg]] · [[BlackArch Tools]]

