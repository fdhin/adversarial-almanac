---
tool: "malheur"
build-system: "autotools"
upstream: "http://www.mlsec.org/malheur/"
recipe: "blackarch"
source: "http://www.mlsec.org/malheur/"
license: "GPL3"
blackarch-pkgver: "0.5.4"
---

# Installing malheur

> A tool for the automatic analyze of malware behavior.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/malheur) (version `0.5.4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S malheur    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.mlsec.org/malheur/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libarchive`
  - `libconfig`
  - `uthash`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var \
      --enable-openmp
  else
    ./configure --prefix=/usr --build=arm --sysconfdir=/etc \
      --localstatedir=/var --enable-openmp
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[malheur]] · [[BlackArch Tools]]

