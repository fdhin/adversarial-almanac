---
tool: "wpprobe"
build-system: "go"
upstream: "https://github.com/Chocapikk/wpprobe"
recipe: "blackarch"
nix-package: "wpprobe"
source: "https://github.com/Chocapikk/wpprobe.git"
license: "MIT"
blackarch-pkgver: "0.12.5.r0.g0b9c573"
---

# Installing wpprobe

> A fast WordPress plugin enumeration tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wpprobe) (version `0.12.5.r0.g0b9c573`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S wpprobe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Chocapikk/wpprobe/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/Chocapikk/wpprobe.git
cd wpprobe
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

Backlinks: [[webapp]] · [[wpprobe]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#wpprobe    # ad-hoc run, no install
nix profile install nixpkgs#wpprobe   # or persist it
```
