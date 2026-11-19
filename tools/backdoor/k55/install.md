---
tool: "k55"
build-system: "other"
upstream: "https://github.com/josh0xA/K55"
recipe: "blackarch"
source: "https://github.com/josh0xA/K55"
license: "MIT"
blackarch-pkgver: "86.b3c4aa9"
---

# Installing k55

> Linux x86_64 Process Injection Utility.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/k55) (version `86.b3c4aa9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S k55    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/josh0xA/K55.git
cd K55
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  g++ -std=c++14 -I . -I ./src *.cc src/*.cc -march=native -w -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[k55]] · [[BlackArch Tools]]

