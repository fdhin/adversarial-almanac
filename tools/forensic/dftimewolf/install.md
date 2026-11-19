---
tool: "dftimewolf"
build-system: "scripts"
upstream: "https://github.com/log2timeline/dftimewolf"
recipe: "blackarch"
source: "https://github.com/log2timeline/dftimewolf"
license: "Apache-2.0"
blackarch-pkgver: "901.cec07dd"
---

# Installing dftimewolf

> Framework for orchestrating forensic collection, processing and data export.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dftimewolf) (version `901.cec07dd`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S dftimewolf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/log2timeline/dftimewolf) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `poetry`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[dftimewolf]] · [[BlackArch Tools]]

