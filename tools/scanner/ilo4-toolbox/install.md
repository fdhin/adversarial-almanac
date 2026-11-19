---
tool: "ilo4-toolbox"
build-system: "go"
upstream: "https://github.com/airbus-seclab/ilo4_toolbox"
recipe: "blackarch"
source: "https://github.com/airbus-seclab/ilo4_toolbox"
license: "GPL2"
blackarch-pkgver: "47.672a5d6"
provides: ["iloscan"]
---

# Installing ilo4-toolbox

> Toolbox for HPE iLO4 analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ilo4-toolbox) (version `47.672a5d6`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S ilo4-toolbox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/airbus-seclab/ilo4_toolbox/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/airbus-seclab/ilo4_toolbox.git
cd ilo4_toolbox
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `ruby`
- ⚠️ Python 2 only deps: python2-capstone, python2-keystone, python2-requests (legacy; needs python2)

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
    -o iloscan ./scanner
```

## Installed layout (per recipe)

- Commands: `/usr/bin/iloscan`

Backlinks: [[scanner]] · [[ilo4-toolbox]] · [[BlackArch Tools]]

