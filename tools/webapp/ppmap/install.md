---
tool: "ppmap"
build-system: "go"
upstream: "https://github.com/kleiton0x00/ppmap"
recipe: "blackarch"
source: "https://github.com/kleiton0x00/ppmap"
license: "GPL3"
blackarch-pkgver: "v1.2.0.r15.g9426af6"
---

# Installing ppmap

> A scanner/exploitation tool written in GO, which leverages client-side Prototype Pollution to XSS by exploiting known gadgets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ppmap) (version `v1.2.0.r15.g9426af6`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ppmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/kleiton0x00/ppmap/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/kleiton0x00/ppmap) (the recipe uses a versioned archive).

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
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[ppmap]] · [[BlackArch Tools]]

