---
tool: "dnsgrep"
build-system: "go"
upstream: "https://github.com/erbbysam/DNSGrep"
recipe: "blackarch"
source: "https://github.com/erbbysam/DNSGrep"
license: "MIT"
blackarch-pkgver: "14.3f4fa7c"
---

# Installing dnsgrep

> A utility for quickly searching presorted DNS names.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsgrep) (version `14.3f4fa7c`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dnsgrep    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/erbbysam/DNSGrep/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/erbbysam/DNSGrep.git
cd DNSGrep
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOFLAGS="-trimpath -buildmode=pie -mod=readonly -modcacherw"

  GOROOT="$srcdir/go" GOPATH="$srcdir" go mod init go.mod
  GOROOT="$srcdir/go" GOPATH="$srcdir" go mod tidy
  GOROOT="$srcdir/go" GOPATH="$srcdir" go mod download
  GOROOT="$srcdir/go" GOPATH="$srcdir" go build \
    $GOFLAGS \
    -ldflags "-s -w" \
    -o $pkgname .

  GOROOT="$srcdir/go" GOPATH="$srcdir" go build \
    $GOFLAGS \
    -ldflags "-s -w" \
    -o $pkgname-server ./experimentalServer/
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[dnsgrep]] · [[BlackArch Tools]]

