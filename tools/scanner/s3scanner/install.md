---
tool: "s3scanner"
build-system: "go"
upstream: "https://github.com/sa7mon/S3Scanner"
recipe: "blackarch"
nix-package: "s3scanner"
source: "https://github.com/sa7mon/S3Scanner"
license: "MIT"
blackarch-pkgver: "v3.1.1.r12.g64a00e2"
---

# Installing s3scanner

> A tool to find open S3 buckets in AWS or other cloud providers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/s3scanner) (version `v3.1.1.r12.g64a00e2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S s3scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/sa7mon/S3Scanner/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/sa7mon/S3Scanner) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $_pkgname

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

Backlinks: [[scanner]] · [[s3scanner]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#s3scanner    # ad-hoc run, no install
nix profile install nixpkgs#s3scanner   # or persist it
```
