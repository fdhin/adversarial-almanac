---
tool: "cisco5crack"
build-system: "autotools"
upstream: "https://github.com/madrisan/cisco5crack"
recipe: "blackarch"
source: "https://github.com/madrisan/cisco5crack"
license: "GPL2"
blackarch-pkgver: "2.c4b228c"
---

# Installing cisco5crack

> Crypt and decrypt the cisco enable 5 passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cisco5crack) (version `2.c4b228c`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S cisco5crack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/madrisan/cisco5crack) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr --sbindir=/usr/bin
  else
    ./configure --build=arm --prefix=/usr --sbindir=/usr/bin
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[cisco5crack]] · [[BlackArch Tools]]

