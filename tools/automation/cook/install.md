---
tool: "cook"
build-system: "go"
upstream: "https://github.com/giteshnxtlvl/cook"
recipe: "blackarch"
source: "https://github.com/giteshnxtlvl/cook"
license: "MIT"
blackarch-pkgver: "v2.3.0.r0.gf72413d"
---

# Installing cook

> Easily create word's permutation and combination to generate complex wordlists and passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cook) (version `v2.3.0.r0.gf72413d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cook    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/giteshnxtlvl/cook/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/giteshnxtlvl/cook) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd "$pkgname/v2/cmd/$pkgname"

  GOPATH="$srcdir" go mod init go.mod
  GOPATH="$srcdir" go mod tidy
  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[cook]] · [[BlackArch Tools]]

