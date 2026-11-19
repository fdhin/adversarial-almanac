---
tool: "mdk3"
build-system: "make"
upstream: "https://aspj.aircrack-ng.org/"
recipe: "blackarch"
aur-package: "mdk3"
source: "https://aspj.aircrack-ng.org/"
license: "GPL2"
blackarch-pkgver: "v6"
kali-package: "mdk3"
kali-version: "6.0-9"
---

# Installing mdk3

> WLAN penetration tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mdk3) (version `v6`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S mdk3    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install mdk3    # Kali package version 6.0-9
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://aspj.aircrack-ng.org/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname-debian-master"

  make -C osdep
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[mdk3]] · [[BlackArch Tools]]

