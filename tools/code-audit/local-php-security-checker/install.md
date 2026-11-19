---
tool: "local-php-security-checker"
build-system: "go"
upstream: "https://github.com/fabpot/local-php-security-checker"
recipe: "blackarch"
aur-package: "local-php-security-checker"
source: "https://github.com/fabpot/local-php-security-checker"
license: "AGPL3"
blackarch-pkgver: "v2.1.3.r1.g1d1fdac"
---

# Installing local-php-security-checker

> A command line tool that checks your PHP application packages with known security vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/local-php-security-checker) (version `v2.1.3.r1.g1d1fdac`, license AGPL3).

## Arch / BlackArch

```bash
sudo pacman -S local-php-security-checker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/fabpot/local-php-security-checker/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/fabpot/local-php-security-checker) (the recipe uses a versioned archive).

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
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[local-php-security-checker]] · [[BlackArch Tools]]

