---
tool: "visualize-logs"
build-system: "python"
upstream: "https://github.com/keithjjones/visualize_logs"
recipe: "blackarch"
source: "https://github.com/keithjjones/visualize_logs"
license: "custom:unknown"
blackarch-pkgver: "118.d2e370e"
---

# Installing visualize-logs

> A Python library and command line tools to provide interactive log visualization.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/visualize-logs) (version `118.d2e370e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S visualize-logs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/keithjjones/visualize_logs.git
cd visualize_logs
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `networkx`, `pandas`, `plotly`, `pydotplus`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `graphviz`
  - `jupyter-nbformat`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[visualize-logs]] · [[BlackArch Tools]]

