---
tool: "thc-smartbrute"
build-system: "autotools"
upstream: "https://www.thc.org/thc-smartbrute/"
recipe: "blackarch"
source: "https://www.thc.org/thc-smartbrute/"
license: "GPL3"
blackarch-pkgver: "1.0"
provides: ["thcsmartbrute"]
---

# Installing thc-smartbrute

> This tool finds undocumented and secret commands implemented in a smartcard.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/thc-smartbrute) (version `1.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S thc-smartbrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://www.thc.org/thc-smartbrute/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `pcsclite`

## Exact build steps from the recipe

```bash
cd "thcsmartbrute-$pkgver"

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr
  else
    ./configure --build=arm --prefix=/usr
  fi

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/thcsmartbrute`

Backlinks: [[cracker]] · [[thc-smartbrute]] · [[BlackArch Tools]]

