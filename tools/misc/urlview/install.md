---
tool: "urlview"
build-system: "make"
upstream: "http://packages.qa.debian.org/u/urlview.html"
recipe: "blackarch"
aur-package: "urlview"
source: "http://packages.qa.debian.org/u/urlview.html"
license: "0BSD, GPL-2.0-or-later"
blackarch-pkgver: "1e"
---

# Installing urlview

> A curses URL parser for text files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/urlview) (version `1e`, license 0BSD, GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S urlview    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://packages.qa.debian.org/u/urlview.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `glibc`
  - `ncurses`

## Exact build steps from the recipe

```bash
cd "$_pkgsrcdir"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[urlview]] · [[BlackArch Tools]]

