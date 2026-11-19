---
tool: "interrogate"
build-system: "make"
upstream: "https://github.com/carmaa/interrogate"
recipe: "blackarch"
source: "https://github.com/carmaa/interrogate"
license: "GPL3"
blackarch-pkgver: "5.eb5f071"
---

# Installing interrogate

> A proof-of-concept tool for identification of cryptographic keys in binary material (regardless of target operating system), first and foremost for memory dump analysis and forensic usage.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/interrogate) (version `5.eb5f071`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S interrogate    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/carmaa/interrogate) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[interrogate]] · [[BlackArch Tools]]

