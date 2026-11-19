---
tool: "mildew"
build-system: "go"
upstream: "https://github.com/daehee/mildew"
recipe: "blackarch"
source: "https://github.com/daehee/mildew"
license: "custom:unknown"
blackarch-pkgver: "11.df49c23"
---

# Installing mildew

> Dotmil subdomain discovery tool that scrapes domains from official DoD website directories and certificate transparency logs.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mildew) (version `11.df49c23`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S mildew    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/daehee/mildew/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/daehee/mildew) (the recipe uses a versioned archive).

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
    -o $pkgname-bin ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[mildew]] · [[BlackArch Tools]]

