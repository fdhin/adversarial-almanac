---
tool: "rtlamr"
build-system: "go"
upstream: "https://github.com/bemasher/rtlamr/"
recipe: "blackarch"
nix-package: "rtlamr"
aur-package: "rtlamr"
source: "https://github.com/bemasher/rtlamr/"
license: "AGPL"
blackarch-pkgver: "197.03369d1"
---

# Installing rtlamr

> An rtl-sdr receiver for smart meters operating in the 900MHz ISM band.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rtlamr) (version `197.03369d1`, license AGPL).

## Arch / BlackArch

```bash
sudo pacman -S rtlamr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/bemasher/rtlamr//releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/bemasher/rtlamr/) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `fftw`

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

Backlinks: [[radio]] · [[rtlamr]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#rtlamr    # ad-hoc run, no install
nix profile install nixpkgs#rtlamr   # or persist it
```
