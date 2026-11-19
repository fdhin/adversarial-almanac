---
tool: "rita"
build-system: "go"
upstream: "https://github.com/activecm/rita"
recipe: "blackarch"
nix-package: "rita"
source: "https://github.com/activecm/rita"
license: "GPL3"
blackarch-pkgver: "v5.1.2.r5.g1317d70"
---

# Installing rita

> Real Intelligence Threat Analytics.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rita) (version `v5.1.2.r5.g1317d70`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S rita    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/activecm/rita/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/activecm/rita) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `bro`
  - `mongodb`

## Exact build steps from the recipe

```bash
cd $pkgname

  VERSION=$(git describe --abbrev=0 --tags)
  EXACT_VERSION=$(git describe --always --long --dirty --tags)

  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w -X github.com/activecm/rita/config.Version=${VERSION} -X github.com/activecm/rita/config.ExactVersion=${EXACT_VERSION}" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[rita]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#rita    # ad-hoc run, no install
nix profile install nixpkgs#rita   # or persist it
```
