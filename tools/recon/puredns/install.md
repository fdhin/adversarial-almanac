---
tool: "puredns"
build-system: "go"
upstream: "https://github.com/d3mondev/puredns"
recipe: "blackarch"
nix-package: "puredns"
aur-package: "puredns"
source: "https://github.com/d3mondev/puredns"
license: "GPL3"
blackarch-pkgver: "v2.1.1.r4.g46bd4c9"
---

# Installing puredns

> Fast domain resolver and subdomain bruteforcing with accurate wildcard filtering.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/puredns) (version `v2.1.1.r4.g46bd4c9`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S puredns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/d3mondev/puredns/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/d3mondev/puredns) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `massdns`

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

Backlinks: [[recon]] · [[puredns]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#puredns    # ad-hoc run, no install
nix profile install nixpkgs#puredns   # or persist it
```
