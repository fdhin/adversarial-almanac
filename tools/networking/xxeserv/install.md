---
tool: "xxeserv"
build-system: "go"
upstream: "https://github.com/staaldraad/xxeserv"
recipe: "blackarch"
source: "https://github.com/staaldraad/xxeserv"
license: "custom:unknown"
blackarch-pkgver: "14.2c7f444"
---

# Installing xxeserv

> A mini webserver with FTP support for XXE payloads.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xxeserv) (version `14.2c7f444`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S xxeserv    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/staaldraad/xxeserv/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/staaldraad/xxeserv) (the recipe uses a versioned archive).

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

Backlinks: [[networking]] · [[xxeserv]] · [[BlackArch Tools]]

