---
tool: "rtlizer"
build-system: "make"
upstream: "https://github.com/csete/rtlizer"
recipe: "blackarch"
source: "https://github.com/csete/rtlizer"
license: "custom:unknown"
blackarch-pkgver: "2.1.r1.g5614163"
---

# Installing rtlizer

> Simple spectrum analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rtlizer) (version `2.1.r1.g5614163`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rtlizer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/csete/rtlizer) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `cairo`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel
  - `rtl-sdr`

## Exact build steps from the recipe

```bash
cd $pkgname

  cd src

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[rtlizer]] · [[BlackArch Tools]]

