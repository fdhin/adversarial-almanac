---
tool: "hacktv"
build-system: "make"
upstream: ""
recipe: "blackarch"
source: ""
license: "GPL3"
blackarch-pkgver: "1.a9e17ed"
---

# Installing hacktv

> Analogue TV transmitter for the HackRF.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hacktv) (version `1.a9e17ed`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S hacktv    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the the upstream site (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ffmpeg`
  - `hackrf`
  - `soapysdr`

## Exact build steps from the recipe

```bash
cd $pkgname

  make -C src/
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[hacktv]] · [[BlackArch Tools]]

