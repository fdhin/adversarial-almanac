---
tool: "vulture-black"
build-system: "python"
upstream: "https://github.com/black-210/VULTURE"
recipe: "blackarch"
source: "https://github.com/black-210/VULTURE"
license: "AGPL-3.0-or-later"
blackarch-pkgver: "147.8b7a1ce"
---

# Installing vulture-black

> Modular intelligence, research, engineering, and automation platform combining RF analysis, signal processing, AI/ML, scientific computing, and more.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vulture-black) (version `147.8b7a1ce`, license AGPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S vulture-black    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/black/VULTURE) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `build`, `cryptography`, `installer`, `matplotlib`, `numpy`, `onnx`, `onnxruntime`, `pandas`, `pillow`, `pydantic`, `pyqt6`, `pyqt6-sip`, `pytest`, `pytest-cov`, `requests`, `scikit-learn`, `scipy`, `setuptools`, `wheel`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $_pkgname

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[vulture-black]] · [[BlackArch Tools]]

