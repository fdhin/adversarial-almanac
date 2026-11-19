---
tool: "hbad"
build-system: "make"
upstream: "http://www.curesec.com/"
recipe: "blackarch"
source: "http://www.curesec.com/"
license: "MIT"
blackarch-pkgver: "1.0"
---

# Installing hbad

> This tool allows you to test clients on the heartbleed bug.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hbad) (version `1.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S hbad    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.curesec.com/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl-1.0`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[hbad]] · [[BlackArch Tools]]

