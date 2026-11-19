---
tool: "jaeles"
build-system: "go"
upstream: "https://github.com/jaeles-project/jaeles"
recipe: "blackarch"
nix-package: "jaeles"
aur-package: "jaeles"
source: "https://github.com/jaeles-project/jaeles"
license: "MIT"
blackarch-pkgver: "234.d40305f"
---

# Installing jaeles

> The Swiss Army knife for automated Web Application Testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jaeles) (version `234.d40305f`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S jaeles    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/jaeles-project/jaeles/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/jaeles-project/jaeles) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[jaeles]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#jaeles    # ad-hoc run, no install
nix profile install nixpkgs#jaeles   # or persist it
```
