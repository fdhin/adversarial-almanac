---
tool: "ftpscout"
build-system: "python"
upstream: "https://github.com/RubenRocha/ftpscout"
recipe: "blackarch"
source: "https://github.com/RubenRocha/ftpscout"
license: "GPL"
blackarch-pkgver: "12.cf1dff1"
---

# Installing ftpscout

> Scans ftps for anonymous access.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ftpscout) (version `12.cf1dff1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S ftpscout    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/RubenRocha/ftpscout) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `netaddr`, `pika`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `rabbitmq`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ftpscout]] · [[BlackArch Tools]]

