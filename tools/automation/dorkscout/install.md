---
tool: "dorkscout"
build-system: "go"
upstream: "https://github.com/R4yGM/dorkscout"
recipe: "blackarch"
nix-package: "dorkscout"
source: "https://github.com/R4yGM/dorkscout"
license: "Apache"
blackarch-pkgver: "1.0.r13.gdd87daf"
---

# Installing dorkscout

> Golang tool to automate google dork scan against the entire internet or specific targets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dorkscout) (version `1.0.r13.gdd87daf`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S dorkscout    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/R4yGM/dorkscout/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/R4yGM/dorkscout) (the recipe uses a versioned archive).

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
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[dorkscout]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dorkscout    # ad-hoc run, no install
nix profile install nixpkgs#dorkscout   # or persist it
```
