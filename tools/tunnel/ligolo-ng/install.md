---
tool: "ligolo-ng"
build-system: "go"
upstream: "https://github.com/nicocha30/ligolo-ng/releases"
recipe: "blackarch"
nix-package: "ligolo-ng"
aur-package: "ligolo-ng"
source: "https://github.com/nicocha30/ligolo-ng/releases"
license: "GPL3"
blackarch-pkgver: "v0.9.2.r0.ge543732"
---

# Installing ligolo-ng

> An advanced, yet simple, tunneling tool that uses a TUN interface.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ligolo-ng) (version `v0.9.2.r0.ge543732`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ligolo-ng    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/nicocha30/ligolo-ng/releases/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/nicocha30/ligolo-ng/releases) (the recipe uses a versioned archive).

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
    -o agent \
    cmd/agent/main.go

  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o proxy \
    cmd/proxy/main.go
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[ligolo-ng]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ligolo-ng    # ad-hoc run, no install
nix profile install nixpkgs#ligolo-ng   # or persist it
```
