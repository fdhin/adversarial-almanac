---
tool: "princeprocessor"
build-system: "make"
upstream: "https://github.com/jsteube/princeprocessor/"
recipe: "blackarch"
source: "https://github.com/jsteube/princeprocessor.git"
license: "MIT"
blackarch-pkgver: "140.4160061"
---

# Installing princeprocessor

> Standalone password candidate generator using the PRINCE algorithm.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/princeprocessor) (version `140.4160061`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S princeprocessor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/jsteube/princeprocessor.git
cd princeprocessor
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `lib32-glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

  make pp64.bin
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[princeprocessor]] · [[BlackArch Tools]]

