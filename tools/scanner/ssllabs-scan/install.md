---
tool: "ssllabs-scan"
build-system: "go"
upstream: "https://github.com/ssllabs/ssllabs-scan"
recipe: "blackarch"
aur-package: "ssllabs-scan"
source: "https://github.com/ssllabs/ssllabs-scan"
license: "APACHE"
blackarch-pkgver: "255.50e3144"
---

# Installing ssllabs-scan

> Command-line client for the SSL Labs APIs

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ssllabs-scan) (version `255.50e3144`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S ssllabs-scan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/ssllabs/ssllabs-scan/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/ssllabs/ssllabs-scan) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[ssllabs-scan]] · [[BlackArch Tools]]

