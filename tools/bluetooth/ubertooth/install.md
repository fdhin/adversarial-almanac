---
tool: "ubertooth"
build-system: "python"
upstream: "https://github.com/greatscottgadgets/ubertooth"
recipe: "blackarch"
nix-package: "ubertooth"
aur-package: "ubertooth"
source: "https://github.com/greatscottgadgets/ubertooth"
license: "GPL-2.0-or-later"
blackarch-pkgver: "2020.12.R1.r35.gc9dfdbd"
kali-package: "ubertooth"
kali-version: "2020.12.R1-2"
---

# Installing ubertooth

> A 2.4 GHz wireless development board suitable for Bluetooth experimentation. Open source hardware and software. Tools only.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ubertooth) (version `2020.12.R1.r35.gc9dfdbd`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S ubertooth    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install ubertooth    # Kali package version 2020.12.R1-2
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/greatscottgadgets/ubertooth) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `libusb1`, `qtpy`, `setuptools`
- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `libbtbb`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname/host/"

  mkdir -p build

  cd build

  cmake \
    -DCMAKE_INSTALL_PREFIX="$pkgdir/usr" \
    -DCMAKE_POLICY_VERSION_MINIMUM=3.5 \
    -DENABLE_PYTHON=FALSE \
    -DUDEV_RULES_PATH="$pkgdir/etc/udev/rules.d" \
    -DINSTALL_UDEV_RULES=TRUE \
    -DUDEV_RULES_GROUP=uucp \
    ..
  make

  cd ../python/ubtbr/

  python setup.py build

  cd ../../python/specan_ui

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/ubertooth`

Backlinks: [[bluetooth]] · [[ubertooth]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ubertooth    # ad-hoc run, no install
nix profile install nixpkgs#ubertooth   # or persist it
```
