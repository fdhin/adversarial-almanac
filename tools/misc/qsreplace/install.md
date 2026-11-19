---
tool: "qsreplace"
build-system: "go"
upstream: "https://github.com/tomnomnom/qsreplace"
recipe: "blackarch"
nix-package: "qsreplace"
source: "https://github.com/tomnomnom/qsreplace"
license: "MIT"
blackarch-pkgver: "5.320475b"
---

# Installing qsreplace

> Accept URLs on stdin, replace all query string values with a user-supplied value, only output each combination of query string parameters once per host and path.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/qsreplace) (version `5.320475b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S qsreplace    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/tomnomnom/qsreplace/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/tomnomnom/qsreplace) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod init go.mod
  GOPATH="$srcdir" go mod tidy
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

Backlinks: [[misc]] · [[qsreplace]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#qsreplace    # ad-hoc run, no install
nix profile install nixpkgs#qsreplace   # or persist it
```
