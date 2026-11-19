---
tool: "adfind"
build-system: "go"
upstream: "https://github.com/sahakkhotsanyan/adfind"
recipe: "blackarch"
source: "https://github.com/sahakkhotsanyan/adfind"
license: "custom:unknown"
blackarch-pkgver: "v1.0.3.r3.g46acf19"
---

# Installing adfind

> Simple admin panel finder for php,js,cgi,asp and aspx admin panels.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/adfind) (version `v1.0.3.r3.g46acf19`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S adfind    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/sahakkhotsanyan/adfind/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/sahakkhotsanyan/adfind) (the recipe uses a versioned archive).

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
    -o $pkgname ./cmd/adfind/main.go
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[adfind]] · [[BlackArch Tools]]

