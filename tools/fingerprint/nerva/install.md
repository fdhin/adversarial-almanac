---
tool: "nerva"
build-system: "go"
upstream: "https://github.com/praetorian-inc/nerva"
recipe: "blackarch"
nix-package: "nerva"
source: "https://github.com/praetorian-inc/nerva"
license: "Apache-2.0"
blackarch-pkgver: "v1.84.0.r0.gea00df8"
---

# Installing nerva

> Fast service fingerprinting CLI for 170+ protocols (TCP/UDP/SCTP).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nerva) (version `v1.84.0.r0.gea00df8`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S nerva    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/praetorian-inc/nerva/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/praetorian-inc/nerva) (the recipe uses a versioned archive).

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
    -o $pkgname ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[nerva]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#nerva    # ad-hoc run, no install
nix profile install nixpkgs#nerva   # or persist it
```
