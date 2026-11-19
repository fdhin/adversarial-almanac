---
tool: "certgraph"
build-system: "go"
upstream: "https://github.com/lanrat/certgraph"
recipe: "blackarch"
nix-package: "certgraph"
source: "https://github.com/lanrat/certgraph"
license: "GPL3"
blackarch-pkgver: "200.7c8dd0d"
---

# Installing certgraph

> Crawl the graph of certificate Alternate Names.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/certgraph) (version `200.7c8dd0d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S certgraph    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/lanrat/certgraph/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/lanrat/certgraph) (the recipe uses a versioned archive).

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

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[certgraph]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#certgraph    # ad-hoc run, no install
nix profile install nixpkgs#certgraph   # or persist it
```
