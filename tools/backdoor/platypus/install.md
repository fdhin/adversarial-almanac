---
tool: "platypus"
build-system: "go"
upstream: "https://github.com/WangYihang/Platypus"
recipe: "blackarch"
aur-package: "platypus"
source: "https://github.com/WangYihang/Platypus"
license: "LGPL-3.0-or-later"
blackarch-pkgver: "v1.5.1.r1117.g075049b"
---

# Installing platypus

> A modern multiple reverse shell sessions manager written in go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/platypus) (version `v1.5.1.r1117.g075049b`, license LGPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S platypus    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/WangYihang/Platypus/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/WangYihang/Platypus) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`, `nodejs`
- **System libraries** (Arch name — translate for your distro):
  - `axel`
  - `go-bindata`
  - `python`
  - `unarchiver`
  - `upx`
  - `yarn`

## Exact build steps from the recipe

```bash
cd $_pkgname

  cd web/frontend && yarn && yarn build && cd ../../
  cd web/ttyd && yarn && yarn build && cd ../../
  go-bindata -pkg resource -o ./internal/utils/resource/resource.go \
    ./internal/runtime/... ./web/ttyd/dist/... ./web/frontend/build/...

  export CGO_CPPFLAGS="${CPPFLAGS}"
  export CGO_CFLAGS="${CFLAGS}"
  export CGO_CXXFLAGS="${CXXFLAGS}"
  export CGO_LDFLAGS="${LDFLAGS}"
  GOPATH="$srcdir" go mod tidy
  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -modcacherw \
    -ldflags "-s -w" \
    -o "$pkgname-admin" "cmd/$pkgname-admin/main.go"
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -modcacherw \
    -ldflags "-s -w" \
    -o "$pkgname-agent" "cmd/$pkgname-agent/main.go"
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -modcacherw \
    -ldflags "-s -w" \
    -o "$pkgname-server" "cmd/$pkgname-server/main.go"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[platypus]] · [[BlackArch Tools]]

