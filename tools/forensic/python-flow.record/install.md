---
tool: "python-flow.record"
build-system: "python"
upstream: "https://github.com/fox-it/flow.record"
recipe: "blackarch"
source: "https://github.com/fox-it/flow.record"
license: "AGPL-3.0-or-later"
blackarch-pkgver: "3.21.r17.gd2d6d8c"
---

# Installing python-flow.record

> Recordization library.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python-flow.record) (version `3.21.r17.gd2d6d8c`, license AGPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S python-flow.record    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/fox-it/flow.record) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `installer`, `maxminddb`, `msgpack`, `pip`, `setuptools`, `setuptools-scm`, `wheel`, `zstandard`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `elasticsearch`
  - `httpx`
  - `lz4`
  - `python`

## Exact build steps from the recipe

```bash
cd $_pkgname

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[python-flow.record]] · [[BlackArch Tools]]

