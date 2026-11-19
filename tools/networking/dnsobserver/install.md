---
tool: "dnsobserver"
build-system: "go"
upstream: "https://github.com/allyomalley/dnsobserver"
recipe: "blackarch"
source: "https://github.com/allyomalley/dnsobserver"
license: "custom:unknown"
blackarch-pkgver: "5.f331482"
---

# Installing dnsobserver

> A handy DNS service written in Go to aid in the detection of several types of blind vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsobserver) (version `5.f331482`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dnsobserver    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/allyomalley/dnsobserver/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/allyomalley/dnsobserver) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

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

Backlinks: [[networking]] · [[dnsobserver]] · [[BlackArch Tools]]

