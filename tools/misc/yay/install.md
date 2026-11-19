---
tool: "yay"
build-system: "go"
upstream: "https://github.com/Jguer/yay"
recipe: "blackarch"
nix-package: "yay"
aur-package: "yay"
source: "https://github.com/Jguer/yay"
license: "GPL-3.0-or-later"
blackarch-pkgver: "13.1.0"
---

# Installing yay

> Yet another yogurt. Pacman wrapper and AUR helper written in go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/yay) (version `13.1.0`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S yay    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Jguer/yay/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/Jguer/yay) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `pacman`

## Exact build steps from the recipe

```bash
export GOPATH="$srcdir"/gopath
  export CGO_CPPFLAGS="${CPPFLAGS}"
  export CGO_CFLAGS="${CFLAGS}"
  export CGO_CXXFLAGS="${CXXFLAGS}"
  export CGO_LDFLAGS="${LDFLAGS}"
  export CGO_ENABLED=1

  cd "$pkgname-$pkgver"

  make VERSION=$pkgver DESTDIR="$pkgdir" PREFIX=/usr build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[yay]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#yay    # ad-hoc run, no install
nix profile install nixpkgs#yay   # or persist it
```
