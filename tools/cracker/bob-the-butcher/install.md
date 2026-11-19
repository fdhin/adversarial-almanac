---
tool: "bob-the-butcher"
build-system: "autotools"
upstream: "https://btb.banquise.net/"
recipe: "blackarch"
source: "https://btb.banquise.net/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.8.1"
---

# Installing bob-the-butcher

> A distributed password cracker package.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bob-the-butcher) (version `0.8.1`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S bob-the-butcher    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://btb.banquise.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libevent`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  autoreconf -i

  if [[ "$CARCH" == "aarch64" ]];
  then
    ./configure --build=arm --prefix=/usr --program-suffix="-sse2"
  else
    ./configure --prefix=/usr --program-suffix="-sse2"
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[bob-the-butcher]] · [[BlackArch Tools]]

