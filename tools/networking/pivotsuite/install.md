---
tool: "pivotsuite"
build-system: "python"
upstream: "https://github.com/RedTeamOperations/PivotSuite"
recipe: "blackarch"
source: "https://github.com/RedTeamOperations/PivotSuite"
license: "GPL3"
blackarch-pkgver: "20.5640666"
---

# Installing pivotsuite

> A portable, platform independent and powerful network pivoting toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pivotsuite) (version `20.5640666`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pivotsuite    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/RedTeamOperations/PivotSuite.git
cd PivotSuite
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `ntlm-auth`, `setuptools`, `six`
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

Backlinks: [[networking]] · [[pivotsuite]] · [[BlackArch Tools]]

