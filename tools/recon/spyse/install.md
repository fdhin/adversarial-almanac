---
tool: "spyse"
build-system: "python"
upstream: "https://github.com/zeropwn/spyse.py"
recipe: "blackarch"
source: "https://github.com/zeropwn/spyse.py"
license: "MIT"
blackarch-pkgver: "47.cd11ba9"
---

# Installing spyse

> Python API wrapper and command-line client for the tools hosted on spyse.com.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spyse) (version `47.cd11ba9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S spyse    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/zeropwn/spyse.py.git
cd spyse.py
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `requests`, `setuptools`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[spyse]] · [[BlackArch Tools]]

