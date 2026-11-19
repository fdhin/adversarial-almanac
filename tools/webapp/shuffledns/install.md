---
tool: "shuffledns"
build-system: "go"
upstream: "https://github.com/projectdiscovery/shuffledns.git"
recipe: "blackarch"
nix-package: "shuffledns"
aur-package: "shuffledns"
source: "https://github.com/projectdiscovery/shuffledns.git"
license: "GPL3"
blackarch-pkgver: "851.bc31821"
---

# Installing shuffledns

> A wrapper around massdns written in GO.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shuffledns) (version `851.bc31821`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S shuffledns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/shuffledns.git/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/shuffledns.git) (the recipe uses a versioned archive).

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
    -o $pkgname ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[shuffledns]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#shuffledns    # ad-hoc run, no install
nix profile install nixpkgs#shuffledns   # or persist it
```
