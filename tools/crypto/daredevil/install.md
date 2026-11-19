---
tool: "daredevil"
build-system: "make"
upstream: "https://github.com/SideChannelMarvels/Daredevil"
recipe: "blackarch"
source: "https://github.com/SideChannelMarvels/Daredevil"
license: "GPL3"
blackarch-pkgver: "42.81cb57f"
---

# Installing daredevil

> A tool to perform (higher-order) correlation power analysis attacks (CPA).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/daredevil) (version `42.81cb57f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S daredevil    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/SideChannelMarvels/Daredevil.git
cd Daredevil
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `clang`
  - `openmp`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[daredevil]] · [[BlackArch Tools]]

