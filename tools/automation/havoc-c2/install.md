---
tool: "havoc-c2"
build-system: "go"
upstream: "https://havocframework.com/"
recipe: "blackarch"
source: "https://musl.cc/x86_64-w64-mingw32-cross.tgz"
license: "GPL3"
blackarch-pkgver: "856.fb67b76"
---

# Installing havoc-c2

> Modern and malleable post-exploitation command and control framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/havoc-c2) (version `856.fb67b76`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S havoc-c2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://havocframework.com//releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
curl -fLO https://musl.cc/x86_64-w64-mingw32-cross.tgz
tar xf x86_64-w64-mingw32-cross.tgz
cd x86_64-w64-mingw32-cross
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `bzip2`
  - `fontconfig`
  - `gdbm`
  - `glu`
  - `gtest`
  - `libffi`
  - `mesa`
  - `mingw-w64-gcc`
  - `nasm`
  - `ncurses`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python`
  - `qt5-base`
  - `qt5-websockets`
  - `readline`
  - `spdlog`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Exact build steps from the recipe

```bash
cd $_pkgname

	cmake --build client/Build -- -j 4

	cd teamserver
  GOPATH="$srcdir" go mod download golang.org/x/sys
  GOPATH="$srcdir" go mod download github.com/ugorji/go
	GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" GO111MODULE="on" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o ../havoc main.go
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[havoc-c2]] · [[BlackArch Tools]]

