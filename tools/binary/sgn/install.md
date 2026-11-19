---
tool: "sgn"
build-system: "go"
upstream: "https://github.com/EgeBalci/sgn"
recipe: "blackarch"
aur-package: "sgn"
source: "https://github.com/EgeBalci/sgn"
license: "MIT"
blackarch-pkgver: "43.d914ab2"
---

# Installing sgn

> Shikata ga nai encoder ported into go with several improvements.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sgn) (version `43.d914ab2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S sgn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/EgeBalci/sgn/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/EgeBalci/sgn) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `capstone`
  - `keystone`

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

Backlinks: [[binary]] · [[sgn]] · [[BlackArch Tools]]

