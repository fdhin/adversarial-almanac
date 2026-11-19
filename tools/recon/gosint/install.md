---
tool: "gosint"
build-system: "go"
upstream: "https://github.com/Nhoya/gOSINT"
recipe: "blackarch"
source: "https://github.com/Nhoya/gOSINT"
license: "GPL3"
blackarch-pkgver: "196.9c86ed2"
---

# Installing gosint

> OSINT framework in Go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gosint) (version `196.9c86ed2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gosint    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Nhoya/gOSINT/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/Nhoya/gOSINT.git
cd gOSINT
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `tesseract`

## Exact build steps from the recipe

```bash
cd $pkgname

  # Package uses a old dependency declaration format and can't be build with the
  # new process. Details of the issue: https://golang.org/doc/go1.4#internalpackages
  # Upstream report: https://github.com/Nhoya/gOSINT/issues/34
  GOPATH="$srcdir/$pkgname" go get github.com/Nhoya/gOSINT/cmd/gosint
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[gosint]] · [[BlackArch Tools]]

