---
tool: "spiderpig-pdffuzzer"
build-system: "scripts"
upstream: "https://code.google.com/p/spiderpig-pdffuzzer/"
recipe: "blackarch"
source: "https://code.google.com/p/spiderpig-pdffuzzer/"
license: "MIT"
blackarch-pkgver: "0.1"
---

# Installing spiderpig-pdffuzzer

> A javascript pdf fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spiderpig-pdffuzzer) (version `0.1`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S spiderpig-pdffuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/p/spiderpig-pdffuzzer/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[spiderpig-pdffuzzer]] · [[BlackArch Tools]]

