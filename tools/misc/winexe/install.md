---
tool: "winexe"
build-system: "autotools"
upstream: "http://sourceforge.net/projects/winexe/"
recipe: "blackarch"
aur-package: "winexe"
source: "http://sourceforge.net/projects/winexe/"
license: "GPL3"
blackarch-pkgver: "1.00"
---

# Installing winexe

> Remotely execute commands on Windows NT/2000/XP/2003 systems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/winexe) (version `1.00`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S winexe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/winexe/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gnutls`
  - `libsasl`
  - `popt`
  - `python2`
  - `talloc`
  - `tdb`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  cd source4

  ./autogen.sh

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    PYTHON_VER=2 ./configure --prefix=/usr --enable-fhs
  else
    PYTHON_VER=2 ./configure --build=arm --prefix=/usr --enable-fhs
  fi

  make basics

  make "bin/$pkgname"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[winexe]] · [[BlackArch Tools]]

