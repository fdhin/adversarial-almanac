---
tool: "pgdbf"
build-system: "autotools"
upstream: "https://github.com/kstrauser/pgdbf"
recipe: "blackarch"
aur-package: "pgdbf"
source: "https://github.com/kstrauser/pgdbf"
license: "GPL3"
blackarch-pkgver: "113.4e84775"
---

# Installing pgdbf

> Convert XBase / FoxPro databases to PostgreSQL

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pgdbf) (version `113.4e84775`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pgdbf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/kstrauser/pgdbf) (the recipe uses a versioned archive).

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

  aclocal
  autoconf
  automake --add-missing

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr
  else
    ./configure --build=arm --prefix=/usr
  fi

	make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[database]] · [[pgdbf]] · [[BlackArch Tools]]

