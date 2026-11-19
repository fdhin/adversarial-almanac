---
tool: "adversarial-robustness-toolbox"
build-system: "python"
upstream: "https://github.com/Trusted-AI/adversarial-robustness-toolbox"
recipe: "blackarch"
source: "https://github.com/Trusted-AI/adversarial-robustness-toolbox"
license: "MIT"
blackarch-pkgver: "1.20.1.r256.gc7973b5"
---

# Installing adversarial-robustness-toolbox

> Python Library for Machine Learning Security.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/adversarial-robustness-toolbox) (version `1.20.1.r256.gc7973b5`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S adversarial-robustness-toolbox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Trusted-AI/adversarial-robustness-toolbox) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `installer`, `numpy`, `pytorch`, `scikit-learn`, `scipy`, `setuptools`, `six`, `tqdm`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[ai]] · [[adversarial-robustness-toolbox]] · [[BlackArch Tools]]

