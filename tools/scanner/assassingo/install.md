---
tool: "assassingo"
build-system: "go"
upstream: "https://github.com/AmyangXYZ/AssassinGo"
recipe: "blackarch"
source: "https://github.com/AmyangXYZ/AssassinGo"
license: "MIT"
blackarch-pkgver: "173.2dd8650"
---

# Installing assassingo

> Web pentest framework for information gathering and vulnerability scanning.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/assassingo) (version `173.2dd8650`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S assassingo    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/AmyangXYZ/AssassinGo/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/AmyangXYZ/AssassinGo) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

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

Backlinks: [[scanner]] · [[assassingo]] · [[BlackArch Tools]]

