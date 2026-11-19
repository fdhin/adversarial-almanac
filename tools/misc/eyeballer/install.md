---
tool: "eyeballer"
build-system: "scripts"
upstream: "https://github.com/BishopFox/eyeballer"
recipe: "blackarch"
source: "https://github.com/BishopFox/eyeballer"
license: "GPL3"
blackarch-pkgver: "143.5299227"
---

# Installing eyeballer

> Convolutional neural network for analyzing pentest screenshots.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eyeballer) (version `143.5299227`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S eyeballer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/BishopFox/eyeballer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `augmentor`, `click`, `jinja`, `matplotlib`, `numpy`, `pandas`, `pillow`, `progressbar`, `scikit-learn`, `tensorflow`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `unzip`
  - `wget`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[eyeballer]] · [[BlackArch Tools]]

