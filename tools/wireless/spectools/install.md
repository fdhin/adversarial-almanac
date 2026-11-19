---
tool: "spectools"
build-system: "make"
upstream: "https://www.kismetwireless.net/spectools/"
recipe: "blackarch"
aur-package: "spectools"
source: "https://www.kismetwireless.net/spectools/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "3.0"
---

# Installing spectools

> Spectrum-Tools is a set of utilities for using the Wi-Spy USB spectrum analyzer hardware.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spectools) (version `3.0`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S spectools    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://www.kismetwireless.net/spectools/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `cairo`
  - `gtk4`
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel
  - `ncurses`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[spectools]] · [[BlackArch Tools]]

