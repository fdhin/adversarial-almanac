---
tool: "sipshock"
build-system: "go"
upstream: "https://github.com/zaf/sipshock"
recipe: "blackarch"
source: "https://github.com/zaf/sipshock"
license: "GPL3"
blackarch-pkgver: "7.6ab5591"
---

# Installing sipshock

> A scanner for SIP proxies vulnerable to Shellshock.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sipshock) (version `7.6ab5591`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sipshock    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/zaf/sipshock/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/zaf/sipshock) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod init go.mod
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

Backlinks: [[scanner]] · [[sipshock]] · [[BlackArch Tools]]

