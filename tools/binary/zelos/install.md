---
tool: "zelos"
build-system: "python"
upstream: "https://github.com/zeropointdynamics/zelos"
recipe: "blackarch"
source: "https://github.com/zeropointdynamics/zelos"
license: "AGPL3"
blackarch-pkgver: "272.506554d"
---

# Installing zelos

> A comprehensive binary emulation and instrumentation platform.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zelos) (version `272.506554d`, license AGPL3).

## Arch / BlackArch

```bash
sudo pacman -S zelos    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/zeropointdynamics/zelos) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `capstone`, `colorama`, `coloredlogs`, `configargparse`, `dnslib`, `dpkt`, `hexdump`, `lark-parser`, `pypacker`, `setuptools`, `sortedcontainers`, `termcolor`, `unicorn`, `verboselogs`, `wheel`, `zebracorn`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `lief`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[zelos]] · [[BlackArch Tools]]

