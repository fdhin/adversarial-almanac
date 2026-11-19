---
tool: "ctypes-sh"
build-system: "autotools"
upstream: "https://github.com/taviso/ctypes.sh"
recipe: "blackarch"
aur-package: "ctypes-sh"
source: "https://github.com/taviso/ctypes.sh"
license: "MIT"
blackarch-pkgver: "165.ca66ae5"
provides: ["ctypes.sh"]
---

# Installing ctypes-sh

> Allows you to call routines in shared libraries from within bash.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ctypes-sh) (version `165.ca66ae5`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ctypes-sh    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/taviso/ctypes.sh
cd ctypes.sh
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `elfutils`
  - `libdwarf`
  - `libelf`
  - `libffi`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./autogen.sh
  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/ctypes.sh`

Backlinks: [[reversing]] · [[ctypes-sh]] · [[BlackArch Tools]]

