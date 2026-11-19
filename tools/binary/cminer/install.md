---
tool: "cminer"
build-system: "other"
upstream: "https://github.com/EgeBalci/Cminer/"
recipe: "blackarch"
source: "https://github.com/EgeBalci/Cminer/"
license: "MIT"
blackarch-pkgver: "25.d766f7e"
---

# Installing cminer

> A tool for enumerating the code caves in PE files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cminer) (version `25.d766f7e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cminer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/EgeBalci/Cminer.git
cd Cminer
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname/src"

  g++ Cminer.cpp -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[cminer]] · [[BlackArch Tools]]

