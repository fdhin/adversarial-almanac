---
tool: "udsim"
build-system: "make"
upstream: "https://github.com/zombieCraig/UDSim/"
recipe: "blackarch"
source: "https://github.com/zombieCraig/UDSim.git"
license: "GPL3"
blackarch-pkgver: "33.b379464"
---

# Installing udsim

> A graphical simulator that can emulate different modules in a vehicle and respond to UDS request.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/udsim) (version `33.b379464`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S udsim    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/zombieCraig/UDSim.git
cd UDSim
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `sdl2`
  - `sdl2_image`
  - `sdl2_ttf`

## Exact build steps from the recipe

```bash
cd UDSim/src

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[udsim]] · [[BlackArch Tools]]

