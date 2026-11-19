---
tool: "rtlsdr-scanner"
build-system: "scripts"
upstream: "https://github.com/EarToEarOak/RTLSDR-Scanner"
recipe: "blackarch"
source: "https://github.com/EarToEarOak/RTLSDR-Scanner"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v1.3.2.r1.g3c032de"
---

# Installing rtlsdr-scanner

> A cross platform Python frequency scanning GUI for the OsmoSDR rtl-sdr library.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rtlsdr-scanner) (version `v1.3.2.r1.g3c032de`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S rtlsdr-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/EarToEarOak/RTLSDR-Scanner.git
cd RTLSDR-Scanner
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `rtl-sdr`
  - `wxpython`
- ⚠️ Python 2 only deps: python2-matplotlib, python2-numpy, python2-pillow, python2-pyrtlsdr, python2-pyserial, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[rtlsdr-scanner]] · [[BlackArch Tools]]

