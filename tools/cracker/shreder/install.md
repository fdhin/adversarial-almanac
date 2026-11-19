---
tool: "shreder"
build-system: "python"
upstream: "https://github.com/EntySec/Shreder"
recipe: "blackarch"
source: "https://github.com/EntySec/Shreder"
license: "MIT"
blackarch-pkgver: "115.a108575"
---

# Installing shreder

> A powerful multi-threaded SSH protocol password bruteforce tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shreder) (version `115.a108575`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S shreder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/EntySec/Shreder.git
cd Shreder
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `paramiko`, `setuptools`
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

Backlinks: [[cracker]] · [[shreder]] · [[BlackArch Tools]]

