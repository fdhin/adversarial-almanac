---
tool: "donut"
build-system: "python"
upstream: "https://github.com/TheWover/donut"
recipe: "blackarch"
aur-package: "donut"
source: "https://github.com/TheWover/donut"
license: "BSD"
blackarch-pkgver: "529.47758d7"
---

# Installing donut

> Generates x86, x64 or AMD64+x86 P.I. shellcode loading .NET Assemblies from memory.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/donut) (version `529.47758d7`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S donut    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/TheWover/donut) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **Build tools**: `gcc`, `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  make

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[backdoor]] · [[donut]] · [[BlackArch Tools]]

