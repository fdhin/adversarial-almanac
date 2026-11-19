---
tool: "netz"
build-system: "go"
upstream: "https://github.com/spectralops/netz"
recipe: "blackarch"
source: "https://github.com/spectralops/netz"
license: "MIT"
blackarch-pkgver: "v0.1.0.r8.g3754e56"
---

# Installing netz

> Discover internet-wide misconfigurations while drinking coffee.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netz) (version `v0.1.0.r8.g3754e56`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S netz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/spectralops/netz/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/spectralops/netz) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[netz]] · [[BlackArch Tools]]

