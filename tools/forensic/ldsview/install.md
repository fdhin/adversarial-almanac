---
tool: "ldsview"
build-system: "go"
upstream: "https://github.com/kgoins/ldsview"
recipe: "blackarch"
source: "https://github.com/kgoins/ldsview"
license: "Apache"
blackarch-pkgver: "47.d8bfcaa"
---

# Installing ldsview

> Offline search tool for LDAP directory dumps in LDIF format.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ldsview) (version `47.d8bfcaa`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S ldsview    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/kgoins/ldsview/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/kgoins/ldsview) (the recipe uses a versioned archive).

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
    -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[ldsview]] · [[BlackArch Tools]]

