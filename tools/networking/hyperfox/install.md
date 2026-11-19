---
tool: "hyperfox"
build-system: "go"
upstream: "https://github.com/xiam/hyperfox"
recipe: "blackarch"
aur-package: "hyperfox"
source: "https://github.com/xiam/hyperfox"
license: "custom:unknown"
blackarch-pkgver: "121.1a8c26f"
---

# Installing hyperfox

> A security tool for proxying and recording HTTP and HTTPs traffic.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hyperfox) (version `121.1a8c26f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hyperfox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/xiam/hyperfox/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/xiam/hyperfox) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

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

Backlinks: [[networking]] · [[hyperfox]] · [[BlackArch Tools]]

