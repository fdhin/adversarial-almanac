---
tool: "dump1090"
build-system: "make"
upstream: "https://github.com/MalcolmRobb/dump1090"
recipe: "blackarch"
source: "https://github.com/MalcolmRobb/dump1090"
license: "custom:BSD"
blackarch-pkgver: "386.bff92c4"
---

# Installing dump1090

> A simple Mode S decoder for RTLSDR devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dump1090) (version `386.bff92c4`, license custom:BSD).

## Arch / BlackArch

```bash
sudo pacman -S dump1090    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/MalcolmRobb/dump1090) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `rtl-sdr`

## Exact build steps from the recipe

```bash
cd $pkgname

  sed -i 's#./public_html#/usr/share/dump1090/public_html#g' "$pkgname.h"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[dump1090]] · [[BlackArch Tools]]

