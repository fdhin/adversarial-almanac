---
tool: "linux-inject"
build-system: "make"
upstream: "https://github.com/gaffe23/linux-inject"
recipe: "blackarch"
source: "https://github.com/gaffe23/linux-inject"
license: "GPL2"
blackarch-pkgver: "100.268d4e4"
---

# Installing linux-inject

> Tool for injecting a shared object into a Linux process.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/linux-inject) (version `100.268d4e4`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S linux-inject    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/gaffe23/linux-inject) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `clang`
  - `gcc-libs`
  - `lib32-gcc-libs`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[linux-inject]] · [[BlackArch Tools]]

