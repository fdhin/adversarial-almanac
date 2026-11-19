---
tool: "amber"
build-system: "go"
upstream: "https://github.com/EgeBalci/Amber"
recipe: "blackarch"
nix-package: "amber"
aur-package: "amber"
source: "https://github.com/EgeBalci/Amber"
license: "custom:unknown"
blackarch-pkgver: "v3.1.r2.gf6eb2dc"
---

# Installing amber

> Reflective PE packer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/amber) (version `v3.1.r2.gf6eb2dc`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S amber    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/EgeBalci/Amber/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/EgeBalci/Amber.git
cd Amber
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `gcc-multilib`
  - `keystone`
  - `mingw-w64-binutils`
  - `mingw-w64-crt`
  - `mingw-w64-gcc`
  - `mingw-w64-headers`
  - `mingw-w64-headers-bootstrap`
  - `mingw-w64-winpthreads`
  - `nasm`

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

Backlinks: [[binary]] · [[amber]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#amber    # ad-hoc run, no install
nix profile install nixpkgs#amber   # or persist it
```
