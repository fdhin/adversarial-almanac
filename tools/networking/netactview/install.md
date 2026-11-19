---
tool: "netactview"
build-system: "autotools"
upstream: "http://netactview.sourceforge.net/index.html"
recipe: "blackarch"
aur-package: "netactview"
source: "http://netactview.sourceforge.net/index.html"
license: "GPL"
blackarch-pkgver: "0.6.4"
provides: ["netactview_polkit"]
---

# Installing netactview

> A graphical network connections viewer similar in functionality to netstat.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netactview) (version `0.6.4`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S netactview    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://netactview.sourceforge.net/index.html) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `intltool`
  - `libglade`
  - `libgtop`
  - `polkit`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --mandir=/usr/share/man

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/netactview_polkit`
- Data: `/usr/share/polkit-1`

Backlinks: [[networking]] · [[netactview]] · [[BlackArch Tools]]

