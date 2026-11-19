---
tool: "jinjector"
build-system: "go"
upstream: "https://github.com/giovanni-iannaccone/Jinjector"
recipe: "blackarch"
source: "https://github.com/giovanni-iannaccone/Jinjector"
license: "GPL-3.0-or-later"
blackarch-pkgver: "25.c1578da"
---

# Installing jinjector

> Joomla modules backdoor injector.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jinjector) (version `25.c1578da`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S jinjector    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/giovanni-iannaccone/Jinjector/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/giovanni-iannaccone/Jinjector) (the recipe uses a versioned archive).

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
    -o $pkgname ./cmd
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[jinjector]] · [[BlackArch Tools]]

