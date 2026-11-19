---
tool: "ftpmap"
build-system: "autotools"
upstream: "http://wcoserver.googlecode.com/files/"
recipe: "blackarch"
source: "http://wcoserver.googlecode.com/files/"
license: "GPL2"
blackarch-pkgver: "52.cbeabbe"
---

# Installing ftpmap

> Scans remote FTP servers to identify what software and what versions they are running.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ftpmap) (version `52.cbeabbe`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S ftpmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://wcoserver.googlecode.com/files/) (the recipe uses a versioned archive).

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
    ./configure --prefix=/usr
  else
    ./configure --prefix=/usr --build=arm
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[ftpmap]] · [[BlackArch Tools]]

