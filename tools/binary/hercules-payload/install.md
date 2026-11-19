---
tool: "hercules-payload"
build-system: "go"
upstream: "https://github.com/EgeBalci/HERCULES"
recipe: "blackarch"
source: "https://github.com/EgeBalci/HERCULES"
license: "MIT"
blackarch-pkgver: "222.2607a3a"
---

# Installing hercules-payload

> A special payload generator that can bypass all antivirus software.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hercules-payload) (version `222.2607a3a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S hercules-payload    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/EgeBalci/HERCULES/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/EgeBalci/HERCULES.git
cd HERCULES
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
    -o $pkgname ./SOURCE
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[hercules-payload]] · [[BlackArch Tools]]

