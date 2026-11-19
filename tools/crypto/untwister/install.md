---
tool: "untwister"
build-system: "make"
upstream: "https://github.com/altf4/untwister"
recipe: "blackarch"
source: "https://github.com/altf4/untwister"
license: "GPL3"
blackarch-pkgver: "119.a42b8f8"
---

# Installing untwister

> Seed recovery tool for PRNGs.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/untwister) (version `119.a42b8f8`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S untwister    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/altf4/untwister) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `boost`
  - `cppunit`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[untwister]] · [[BlackArch Tools]]

