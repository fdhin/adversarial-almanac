---
tool: "stenographer"
build-system: "go"
upstream: "https://github.com/google/stenographer"
recipe: "blackarch"
source: "https://github.com/google/stenographer"
license: "APACHE"
blackarch-pkgver: "486.355604b"
---

# Installing stenographer

> A packet capture solution which aims to quickly spool all packets to disk, then provide simple, fast access to subsets of those packets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stenographer) (version `486.355604b`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S stenographer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/google/stenographer/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/google/stenographer) (the recipe uses a versioned archive).

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

Backlinks: [[sniffer]] · [[stenographer]] · [[BlackArch Tools]]

