---
tool: "mantra"
build-system: "go"
upstream: "https://github.com/MrEmpy/mantra"
recipe: "blackarch"
nix-package: "mantra"
aur-package: "mantra"
source: "https://github.com/MrEmpy/mantra"
license: "GPL3"
blackarch-pkgver: "v3.1.r5.g6026816"
---

# Installing mantra

> Hunt down API key leaks in JS files and pages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mantra) (version `v3.1.r5.g6026816`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S mantra    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/MrEmpy/mantra/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/MrEmpy/mantra) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[mantra]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#mantra    # ad-hoc run, no install
nix profile install nixpkgs#mantra   # or persist it
```
