---
tool: "mosca"
build-system: "make"
upstream: "https://github.com/CoolerVoid/Mosca"
recipe: "blackarch"
aur-package: "mosca"
source: "https://github.com/CoolerVoid/Mosca"
license: "GPL3"
blackarch-pkgver: "130.a7e725d"
---

# Installing mosca

> Static analysis tool to find bugs like a grep unix command.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mosca) (version `130.a7e725d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S mosca    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/CoolerVoid/Mosca.git
cd Mosca
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `pcre`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[mosca]] · [[BlackArch Tools]]

