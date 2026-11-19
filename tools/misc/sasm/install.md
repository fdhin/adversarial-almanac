---
tool: "sasm"
build-system: "make"
upstream: "https://github.com/Dman95/SASM"
recipe: "blackarch"
aur-package: "sasm"
source: "https://github.com/Dman95/SASM"
license: "GPL-3.0-or-later"
blackarch-pkgver: "3.16.0"
provides: ["fasm", "listing"]
---

# Installing sasm

> A simple crossplatform IDE for NASM, MASM, GAS and FASM assembly languages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sasm) (version `3.16.0`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S sasm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/Dman95/SASM) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gdb`
  - `libxcb`
  - `mesa`
  - `nasm`
  - `qt5-base`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  qmake QMAKE_CXXFLAGS="$CXXFLAGS -Wa,--noexecstack -D_REENTRANT" \
        QMAKE_LFLAGS="$LDFLAGS -Wl,-z,noexecstack"

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/fasm`, `/usr/bin/listing`

Backlinks: [[misc]] · [[sasm]] · [[BlackArch Tools]]

