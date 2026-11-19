---
tool: "avet"
build-system: "scripts"
upstream: "https://github.com/govolution/avet"
recipe: "blackarch"
source: "https://github.com/govolution/avet"
license: "GPL3"
blackarch-pkgver: "560.6da43bf"
---

# Installing avet

> AntiVirus Evasion Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/avet) (version `560.6da43bf`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S avet    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/govolution/avet) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `certifi`, `charset-normalizer`, `contourpy`, `cycler`, `dateutil`, `fonttools`, `idna`, `joblib`, `kiwisolver`, `lief`, `matplotlib`, `mpmath`, `networkx`, `pillow`, `requests`, `scipy`, `sympy`, `threadpoolctl`, `typing_extensions`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `wine`
  - `wine-mono`
  - `wine_gecko`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[avet]] · [[BlackArch Tools]]

