---
tool: "cleverhans"
build-system: "python"
upstream: "https://github.com/cleverhans-lab/cleverhans"
recipe: "blackarch"
source: "https://github.com/cleverhans-lab/cleverhans"
license: "MIT"
blackarch-pkgver: "v4.0.0.r7.g574efc1"
---

# Installing cleverhans

> Python library to benchmark machine learning systems vulnerability to adversarial examples.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cleverhans) (version `v4.0.0.r7.g574efc1`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cleverhans    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/cleverhans-lab/cleverhans) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `absl`, `build`, `easydict`, `installer`, `joblib`, `matplotlib`, `mnist`, `nose`, `numpy`, `pycodestyle`, `pytorch`, `scipy`, `setuptools`, `six`, `tensorflow-probability`, `tqdm`, `wheel`
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

Backlinks: [[ai]] · [[cleverhans]] · [[BlackArch Tools]]

