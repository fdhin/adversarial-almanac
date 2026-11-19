---
tool: "gau"
build-system: "go"
upstream: "https://github.com/lc/gau/"
recipe: "blackarch"
nix-package: "gau"
aur-package: "gau"
source: "https://github.com/lc/gau/"
license: "custom:unknown"
blackarch-pkgver: "180.4eabace"
---

# Installing gau

> Fetch known URLs from AlienVault's Open Threat Exchange, the Wayback Machine, and Common Crawl.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gau) (version `180.4eabace`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gau    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/lc/gau//releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/lc/gau/) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd "$pkgname/cmd/$pkgname"

  GOPATH="$srcdir" go mod tidy
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

Backlinks: [[webapp]] · [[gau]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#gau    # ad-hoc run, no install
nix profile install nixpkgs#gau   # or persist it
```
