---
tool: "fortiscan"
build-system: "go"
upstream: "https://github.com/anasbousselham/fortiscan"
recipe: "blackarch"
source: "https://github.com/anasbousselham/fortiscan"
license: "custom:unknown"
blackarch-pkgver: "0.7.r9.gbd8e460"
---

# Installing fortiscan

> A high performance FortiGate SSL-VPN vulnerability scanning and exploitation tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fortiscan) (version `0.7.r9.gbd8e460`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S fortiscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/anasbousselham/fortiscan/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/anasbousselham/fortiscan) (the recipe uses a versioned archive).

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

  GOPATH="$srcdir" go mod init go.mod
  GOPATH="$srcdir" go mod tidy
  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname "${pkgname}_v0.5.go"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[fortiscan]] · [[BlackArch Tools]]

