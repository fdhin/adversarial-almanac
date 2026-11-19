---
tool: "pspy"
build-system: "go"
upstream: "https://github.com/DominicBreuker/pspy"
recipe: "blackarch"
nix-package: "pspy"
aur-package: "pspy"
source: "https://github.com/DominicBreuker/pspy"
license: "GPL3"
blackarch-pkgver: "169.b8e247e"
---

# Installing pspy

> Monitor linux processes without root permissions.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pspy) (version `169.b8e247e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pspy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/DominicBreuker/pspy/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/DominicBreuker/pspy) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  VERSION=$(git describe --tags --always)
  BUILD_SHA=$(git rev-parse HEAD)

  GOPATH="$srcdir" go mod init go.mod
  GOPATH="$srcdir" go mod tidy
  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w -X 'main.version=${VERSION}' -X 'main.commit=${BUILD_SHA}'" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[pspy]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pspy    # ad-hoc run, no install
nix profile install nixpkgs#pspy   # or persist it
```
