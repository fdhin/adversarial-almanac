---
tool: "interlace"
build-system: "python"
upstream: "https://github.com/codingo/Interlace/releases"
recipe: "blackarch"
source: "https://github.com/codingo/Interlace/releases"
license: "GPL3"
blackarch-pkgver: "356.b9eb99f"
---

# Installing interlace

> Easily turn single threaded command line applications into a fast, multi-threaded application with CIDR and glob support.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/interlace) (version `356.b9eb99f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S interlace    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/codingo/Interlace.git
cd Interlace
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `colorclass`, `netaddr`, `setuptools`, `tqdm`
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

Backlinks: [[networking]] · [[interlace]] · [[BlackArch Tools]]

