---
tool: "simtrace2"
build-system: "autotools"
upstream: "https://osmocom.org/projects/simtrace2/wiki"
recipe: "blackarch"
aur-package: "simtrace2"
source: "git://git.osmocom.org/simtrace2.git"
license: "GPL"
blackarch-pkgver: "1122.936009e"
---

# Installing simtrace2

> Host utilities to communicate with SIMtrace2 USB Devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/simtrace2) (version `1122.936009e`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S simtrace2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone git://git.osmocom.org/simtrace2.git
cd simtrace2
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libosmocore`
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel
  - `lksctp-tools`
  - `pcsclite`
  - `talloc`

## Exact build steps from the recipe

```bash
cd "$pkgname/host"

  autoreconf -fi
  ./configure
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[simtrace2]] · [[BlackArch Tools]]

