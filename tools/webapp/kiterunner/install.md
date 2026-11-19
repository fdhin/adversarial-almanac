---
tool: "kiterunner"
build-system: "go"
upstream: "https://github.com/assetnote/kiterunner"
recipe: "blackarch"
nix-package: "kiterunner"
source: "https://wordlists-cdn.assetnote.io/data/kiterunner/routes-large.kite.tar.gz"
license: "AGPL3"
blackarch-pkgver: "19.7d5824c"
---

# Installing kiterunner

> Contextual Content Discovery Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kiterunner) (version `19.7d5824c`, license AGPL3).

## Arch / BlackArch

```bash
sudo pacman -S kiterunner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/assetnote/kiterunner/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
curl -fLO https://wordlists-cdn.assetnote.io/data/kiterunner/routes-large.kite.tar.gz
tar xf routes-large.kite.tar.gz
cd routes-large.kite
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
    -o $pkgname ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[kiterunner]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#kiterunner    # ad-hoc run, no install
nix profile install nixpkgs#kiterunner   # or persist it
```
