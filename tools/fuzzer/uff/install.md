---
tool: "uff"
build-system: "go"
upstream: "https://github.com/sw33tLie/uff"
recipe: "blackarch"
source: "https://github.com/sw33tLie/uff"
license: "MIT"
blackarch-pkgver: "46.93fea53"
---

# Installing uff

> Unleashed ffuf. A fork of ffuf with more functions & a modified HTTP stack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uff) (version `46.93fea53`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S uff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/sw33tLie/uff/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/sw33tLie/uff) (the recipe uses a versioned archive).

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

- Installs a single static binary: `/usr/bin/uff`
- Config example and markdown docs under `/usr/share/doc/uff/`

Backlinks: [[fuzzer]] · [[uff]] · [[BlackArch Tools]]

