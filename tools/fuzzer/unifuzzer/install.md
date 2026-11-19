---
tool: "unifuzzer"
build-system: "make"
upstream: "https://github.com/rk700/uniFuzzer"
recipe: "blackarch"
source: "https://github.com/rk700/uniFuzzer"
license: "GPL3"
blackarch-pkgver: "5.3385a3b"
---

# Installing unifuzzer

> A fuzzing tool for closed-source binaries based on Unicorn and LibFuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/unifuzzer) (version `5.3385a3b`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S unifuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/rk700/uniFuzzer.git
cd uniFuzzer
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `clang`
  - `glibc`
  - `unicorn`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[unifuzzer]] · [[BlackArch Tools]]

