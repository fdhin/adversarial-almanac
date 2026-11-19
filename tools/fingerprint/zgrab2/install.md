---
tool: "zgrab2"
build-system: "go"
upstream: "https://github.com/zmap/zgrab2"
recipe: "blackarch"
nix-package: "zgrab2"
aur-package: "zgrab2"
source: "https://github.com/zmap/zgrab2"
license: "APACHE, ISC, custom:unknown"
blackarch-pkgver: "v1.0.0.r93.g835aa53"
---

# Installing zgrab2

> Fast Application Layer Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zgrab2) (version `v1.0.0.r93.g835aa53`, license APACHE, ISC, custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S zgrab2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/zmap/zgrab2/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/zmap/zgrab2) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

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

Backlinks: [[fingerprint]] · [[zgrab2]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#zgrab2    # ad-hoc run, no install
nix profile install nixpkgs#zgrab2   # or persist it
```
