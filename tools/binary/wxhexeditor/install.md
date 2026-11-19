---
tool: "wxhexeditor"
build-system: "make"
upstream: "http://wxhexeditor.sourceforge.net/"
recipe: "blackarch"
nix-package: "wxhexeditor"
aur-package: "wxhexeditor"
source: "http://wxhexeditor.sourceforge.net/"
license: "GPL"
blackarch-pkgver: "v0.24.r109.gf439d8f"
---

# Installing wxhexeditor

> A free hex editor / disk editor for Linux, Windows and MacOSX.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wxhexeditor) (version `v0.24.r109.gf439d8f`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S wxhexeditor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/EUA/wxHexEditor.git
cd wxHexEditor
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gcc-libs`
  - `mhash`
  - `python`
  - `python2`
  - `wxwidgets-gtk3`

## Exact build steps from the recipe

```bash
cd $pkgname

  export CFLAGS="-O2 -Wall -Wno-error -Wno-implicit-function-declaration -std=gnu89"
  export CXXFLAGS="${CXXFLAGS} -O2 -Wall -Wno-error -Wno-implicit-function-declaration -std=gnu89"

  if [[ $CARCH == "aarch64" ]]; then
    export CC="gcc"
    export CXX="g++"
    export HOST="aarch64-linux-gnu"
    export CHOST="aarch64-linux-gnu"
    export CBUILD="aarch64-linux-gnu"
    export CFLAGS+=" -march=armv8-a"
    export CXXFLAGS+=" -march=armv8-a"
  fi

  make -s WXCONFIG='/usr/bin/wx-config' CXXFLAGS="${CXXFLAGS/-fno-rtti/}"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[wxhexeditor]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#wxhexeditor    # ad-hoc run, no install
nix profile install nixpkgs#wxhexeditor   # or persist it
```
