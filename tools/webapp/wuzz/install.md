---
tool: "wuzz"
build-system: "go"
upstream: "https://github.com/asciimoo/wuzz"
recipe: "blackarch"
nix-package: "wuzz"
aur-package: "wuzz"
source: "https://github.com/asciimoo/wuzz"
license: "AGPL3"
blackarch-pkgver: "v0.5.0.r24.g82195c6"
---

# Installing wuzz

> Interactive cli tool for HTTP inspection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wuzz) (version `v0.5.0.r24.g82195c6`, license AGPL3).

## Arch / BlackArch

```bash
sudo pacman -S wuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/asciimoo/wuzz/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/asciimoo/wuzz) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[wuzz]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#wuzz    # ad-hoc run, no install
nix profile install nixpkgs#wuzz   # or persist it
```
