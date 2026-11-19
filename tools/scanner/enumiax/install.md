---
tool: "enumiax"
build-system: "make"
upstream: "https://sourceforge.net/projects/enumiax/"
recipe: "blackarch"
source: "https://sourceforge.net/projects/enumiax/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1.0"
kali-package: "enumiax"
kali-version: "0.4a-1kali4"
---

# Installing enumiax

> An IAX enumerator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/enumiax) (version `1.0`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S enumiax    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install enumiax    # Kali package version 0.4a-1kali4
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://sourceforge.net/projects/enumiax/) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[enumiax]] · [[BlackArch Tools]]

