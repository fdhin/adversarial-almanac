---
tool: "bios_memimage"
build-system: "make"
upstream: "http://citp.princeton.edu/memory/code/"
recipe: "blackarch"
source: "http://citp.princeton.edu/memory/code/"
license: "BSD"
blackarch-pkgver: "1.2"
provides: ["pxedump", "usbdump"]
---

# Installing bios_memimage

> A tool to dump RAM contents to disk (aka cold boot attack).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bios_memimage) (version `1.2`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S bios_memimage    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://citp.princeton.edu/memory/code/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd $pkgname

  machine="`uname -m`"

  if [ "$machine" = "x86_64" ]
  then
    make -f Makefile.64 -C stand
    make -f Makefile.64
  else
    make
  fi
```

## Installed layout (per recipe)

- Commands: `/usr/bin/pxedump`, `/usr/bin/usbdump`

Backlinks: [[cracker]] · [[bios_memimage]] · [[BlackArch Tools]]

