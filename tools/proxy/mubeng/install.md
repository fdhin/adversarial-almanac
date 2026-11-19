---
tool: "mubeng"
build-system: "go"
upstream: "https://github.com/kitabisa/mubeng"
recipe: "blackarch"
nix-package: "mubeng"
source: "https://github.com/kitabisa/mubeng"
license: "APACHE"
blackarch-pkgver: "274.963b14d"
---

# Installing mubeng

> An incredibly fast proxy checker & IP rotator with ease.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mubeng) (version `274.963b14d`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S mubeng    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/kitabisa/mubeng/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/kitabisa/mubeng) (the recipe uses a versioned archive).

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
    -ldflags "-s -w -X ktbs.dev/$pkgname/common.Version=v$pkgver" \
    -o $pkgname ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[mubeng]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#mubeng    # ad-hoc run, no install
nix profile install nixpkgs#mubeng   # or persist it
```
