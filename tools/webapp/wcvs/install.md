---
tool: "wcvs"
build-system: "go"
upstream: "https://github.com/Hackmanit/Web-Cache-Vulnerability-Scanner"
recipe: "blackarch"
source: "https://github.com/Hackmanit/Web-Cache-Vulnerability-Scanner"
license: "Apache"
blackarch-pkgver: "2.0.0.r2.g75159ea"
---

# Installing wcvs

> Web Cache Vulnerability Scanner is a Go-based CLI tool for testing for web cache poisoning.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wcvs) (version `2.0.0.r2.g75159ea`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S wcvs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Hackmanit/Web-Cache-Vulnerability-Scanner/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/Hackmanit/Web-Cache-Vulnerability-Scanner
cd Web-Cache-Vulnerability-Scanner
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

Backlinks: [[webapp]] · [[wcvs]] · [[BlackArch Tools]]

