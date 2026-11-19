---
tool: "gg-images"
build-system: "go"
upstream: "https://codeberg.org/nanoory/gg_images"
recipe: "blackarch"
source: "https://codeberg.org/nanoory/gg_images"
license: "GPL3"
blackarch-pkgver: "47.1023f9f"
---

# Installing gg-images

> The application was created to allow anyone to easily download profile pictures from GG.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gg-images) (version `47.1023f9f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gg-images    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://codeberg.org/nanoory/gg_images/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://codeberg.org/nanoory/gg_images.git
cd gg_images
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `libglvnd`
  - `libxcursor`
  - `libxinerama`
  - `libxrandr`
  - `xorg-xinput`

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
    -o $pkgname *.go
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[gg-images]] · [[BlackArch Tools]]

