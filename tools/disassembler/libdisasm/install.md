---
tool: "libdisasm"
build-system: "autotools"
upstream: "http://bastard.sourceforge.net/libdisasm.html"
recipe: "blackarch"
aur-package: "libdisasm"
source: "http://bastard.sourceforge.net/libdisasm.html"
license: "custom:Artistic"
blackarch-pkgver: "0.23"
---

# Installing libdisasm

> A disassembler library.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/libdisasm) (version `0.23`, license custom:Artistic).

## Arch / BlackArch

```bash
sudo pacman -S libdisasm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://bastard.sourceforge.net/libdisasm.html) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr
  else
    ./configure --prefix=/usr --build=arm
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[disassembler]] · [[libdisasm]] · [[BlackArch Tools]]

