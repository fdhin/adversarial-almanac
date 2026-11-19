---
tool: "s3-fuzzer"
build-system: "go"
upstream: "https://github.com/petermbenjamin/s3-fuzzer"
recipe: "blackarch"
source: "https://github.com/petermbenjamin/s3-fuzzer"
license: "BSD"
blackarch-pkgver: "4.0a2a6f0"
---

# Installing s3-fuzzer

> A concurrent, command-line AWS S3 Fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/s3-fuzzer) (version `4.0a2a6f0`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S s3-fuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/petermbenjamin/s3-fuzzer/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/petermbenjamin/s3-fuzzer) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod init go.mod
  GOPATH="$srcdir" go mod edit -replace \
    github.com/petermbenjamin/go-open@v0.1.1=github.com/pbnj/go-open@v0.1.1
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

Backlinks: [[fuzzer]] · [[s3-fuzzer]] · [[BlackArch Tools]]

