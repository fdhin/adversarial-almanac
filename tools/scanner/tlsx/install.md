---
tool: "tlsx"
build-system: "go"
upstream: "https://github.com/projectdiscovery/tlsx"
recipe: "blackarch"
nix-package: "tlsx"
aur-package: "tlsx"
source: "https://github.com/projectdiscovery/tlsx"
license: "MIT"
blackarch-pkgver: "v1.4.3.r3.gf77159c"
---

# Installing tlsx

> TLS grabber focused on TLS based data collection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tlsx) (version `v1.4.3.r3.gf77159c`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S tlsx    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/tlsx/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/tlsx) (the recipe uses a versioned archive).

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
    -o $pkgname ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[tlsx]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#tlsx    # ad-hoc run, no install
nix profile install nixpkgs#tlsx   # or persist it
```
