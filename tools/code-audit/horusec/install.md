---
tool: "horusec"
build-system: "go"
upstream: "https://github.com/ZupIT/horusec"
recipe: "blackarch"
source: "https://github.com/ZupIT/horusec"
license: "Apache"
blackarch-pkgver: "v2.9.0.beta.2.r1.g873d4104"
---

# Installing horusec

> Static code analysis to identify security flaws for many languages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/horusec) (version `v2.9.0.beta.2.r1.g873d4104`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S horusec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/ZupIT/horusec/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/ZupIT/horusec) (the recipe uses a versioned archive).

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
    -o $pkgname ./cmd/app
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[horusec]] · [[BlackArch Tools]]

