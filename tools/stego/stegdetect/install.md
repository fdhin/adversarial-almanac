---
tool: "stegdetect"
build-system: "autotools"
upstream: "https://github.com/redNixon/stegdetect"
recipe: "blackarch"
source: "https://github.com/redNixon/stegdetect"
license: "BSD"
blackarch-pkgver: "20.28a4f07"
---

# Installing stegdetect

> An automated tool for detecting steganographic content in images.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stegdetect) (version `20.28a4f07`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S stegdetect    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/redNixon/stegdetect) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `automake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `gdk-pixbuf2`

## Exact build steps from the recipe

```bash
cd $pkgname

  autoreconf -f -i # regen new confs

  if [ $CARCH = "x86_64" ]
  then
    linux32 ./configure --prefix=/usr
    linux32 make
  else
    ./configure --prefix=/usr
    make
  fi
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`, `/usr/share/stegbreak`

Backlinks: [[stego]] · [[stegdetect]] · [[BlackArch Tools]]

