---
tool: "kalibrate-rtl"
build-system: "autotools"
upstream: "https://github.com/steve-m/kalibrate-rtl"
recipe: "blackarch"
nix-package: "kalibrate-rtl"
source: "https://github.com/steve-m/kalibrate-rtl"
license: "custom"
blackarch-pkgver: "69.340003e"
kali-package: "kalibrate-rtl"
kali-version: "0.4.1+git20191125-0kali2+b2"
---

# Installing kalibrate-rtl

> Fork of http://thre.at/kalibrate/ for use with rtl-sdr devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kalibrate-rtl) (version `69.340003e`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S kalibrate-rtl    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install kalibrate-rtl    # Kali package version 0.4.1+git20191125-0kali2+b2
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/steve-m/kalibrate-rtl) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `fftw`
  - `rtl-sdr`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./bootstrap

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[kalibrate-rtl]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#kalibrate-rtl    # ad-hoc run, no install
nix profile install nixpkgs#kalibrate-rtl   # or persist it
```
