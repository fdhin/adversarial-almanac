---
tool: "iaxflood"
build-system: "make"
upstream: "https://www.hackingexposedvoip.com/"
recipe: "blackarch"
source: "https://www.hackingexposedvoip.com/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.2"
kali-package: "iaxflood"
kali-version: "0.1-1kali5"
---

# Installing iaxflood

> IAX flooder.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/iaxflood) (version `0.2`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S iaxflood    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install iaxflood    # Kali package version 0.1-1kali5
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://www.hackingexposedvoip.com/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[iaxflood]] · [[BlackArch Tools]]

