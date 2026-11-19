---
tool: "naabu"
build-system: "go"
upstream: "https://github.com/projectdiscovery/naabu"
recipe: "blackarch"
nix-package: "naabu"
aur-package: "naabu"
source: "https://github.com/projectdiscovery/naabu"
license: "GPL3"
blackarch-pkgver: "v2.6.1.r143.g6f69a8b"
---

# Installing naabu

> A fast port scanner written in go with focus on reliability and simplicity.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/naabu) (version `v2.6.1.r143.g6f69a8b`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S naabu    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/naabu/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/naabu) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

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
    -o $pkgname "./cmd/$pkgname"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[naabu]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#naabu    # ad-hoc run, no install
nix profile install nixpkgs#naabu   # or persist it
```
