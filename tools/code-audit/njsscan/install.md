---
tool: "njsscan"
build-system: "python"
upstream: "https://pypi.org/project/njsscan/#files"
recipe: "blackarch"
source: "https://pypi.org/project/njsscan/#files"
license: "GPL2"
blackarch-pkgver: "0.3.1"
---

# Installing njsscan

> A static application testing (SAST) tool that can find insecure code patterns in your node.js applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/njsscan) (version `0.3.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S njsscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/njsscan/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `jschema-to-python`, `jsonpickle`, `libsast`, `pbr`, `ruamel-yaml`, `sarif-om`, `setuptools`, `tabulate`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `semgrep`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[njsscan]] · [[BlackArch Tools]]

