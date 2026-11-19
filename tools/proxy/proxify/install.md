---
tool: "proxify"
build-system: "go"
upstream: "https://github.com/projectdiscovery/proxify"
recipe: "blackarch"
nix-package: "proxify"
aur-package: "proxify"
source: "https://github.com/projectdiscovery/proxify"
license: "MIT"
blackarch-pkgver: "905.01804da"
---

# Installing proxify

> Swiss Army knife Proxy tool for HTTP/HTTPS traffic capture, manipulation, and replay on the go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/proxify) (version `905.01804da`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S proxify    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/proxify/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/proxify) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd "$pkgname"

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

Backlinks: [[proxy]] · [[proxify]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#proxify    # ad-hoc run, no install
nix profile install nixpkgs#proxify   # or persist it
```
