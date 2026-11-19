---
tool: "navgix"
build-system: "go"
upstream: "https://github.com/hakaioffsec/navgix"
recipe: "blackarch"
source: "https://github.com/hakaioffsec/navgix"
license: "custom:unknown"
blackarch-pkgver: "3.6f25c98"
---

# Installing navgix

> Multi-threaded golang tool that will check for nginx alias traversal vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/navgix) (version `3.6f25c98`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S navgix    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/hakaioffsec/navgix/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/hakaioffsec/navgix) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod tidy
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

Backlinks: [[scanner]] · [[navgix]] · [[BlackArch Tools]]

