---
tool: "modlishka"
build-system: "go"
upstream: "https://github.com/drk1wi/Modlishka"
recipe: "blackarch"
source: "https://github.com/drk1wi/Modlishka"
license: "custom:unknown"
blackarch-pkgver: "v.1.1.1.r11.g214dd6c"
---

# Installing modlishka

> A powerful and flexible HTTP reverse proxy.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/modlishka) (version `v.1.1.1.r11.g214dd6c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S modlishka    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/drk1wi/Modlishka/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/drk1wi/Modlishka) (the recipe uses a versioned archive).

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

Backlinks: [[proxy]] · [[modlishka]] · [[BlackArch Tools]]

