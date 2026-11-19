---
tool: "cmsmap"
build-system: "python"
upstream: "https://www.dionach.com/blog/cmsmap-%E2%80%93-a-simple-cms-vulnerability-scanner"
recipe: "blackarch"
source: "https://www.dionach.com/blog/cmsmap-%E2%80%93-a-simple-cms-vulnerability-scanner"
license: "GPL3"
blackarch-pkgver: "8.59dd0e2"
---

# Installing cmsmap

> A python open source Content Management System scanner that automates the process of detecting security flaws of the most popular CMSs.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cmsmap) (version `8.59dd0e2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cmsmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Dionach/CMSmap.git
cd CMSmap
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `exploitdb`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[cmsmap]] · [[BlackArch Tools]]

