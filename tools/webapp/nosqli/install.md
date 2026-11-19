---
tool: "nosqli"
build-system: "go"
upstream: "https://github.com/Charlie-belmer/nosqli"
recipe: "blackarch"
nix-package: "nosqli"
source: "https://github.com/Charlie-belmer/nosqli"
license: "GPL3"
blackarch-pkgver: "37.6fce3eb"
---

# Installing nosqli

> NoSQL scanner and injector.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nosqli) (version `37.6fce3eb`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S nosqli    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Charlie-belmer/nosqli/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/Charlie-belmer/nosqli) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[nosqli]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#nosqli    # ad-hoc run, no install
nix profile install nixpkgs#nosqli   # or persist it
```
