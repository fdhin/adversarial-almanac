---
tool: "gps-sdr-sim"
build-system: "make"
upstream: "https://github.com/osqzss/gps-sdr-sim"
recipe: "blackarch"
source: "https://github.com/osqzss/gps-sdr-sim"
license: "MIT"
blackarch-pkgver: "224.28ca29a"
provides: ["gps-sdr-sim-uhd"]
---

# Installing gps-sdr-sim

> Software-Defined GPS Signal Simulator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gps-sdr-sim) (version `224.28ca29a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gps-sdr-sim    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/osqzss/gps-sdr-sim) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bladerf`
  - `gnuradio`
  - `gnuradio-osmosdr`
  - `hackrf`
  - `python2`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/gps-sdr-sim-uhd`

Backlinks: [[radio]] · [[gps-sdr-sim]] · [[BlackArch Tools]]

