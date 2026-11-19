---
tool: "websockify"
build-system: "python"
upstream: "http://github.com/kanaka/websockify"
recipe: "blackarch"
aur-package: "websockify"
source: "http://github.com/kanaka/websockify"
license: "LGPL3"
blackarch-pkgver: "998.1e284ea"
---

# Installing websockify

> WebSocket to TCP proxy/bridge.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/websockify) (version `998.1e284ea`, license LGPL3).

## Arch / BlackArch

```bash
sudo pacman -S websockify    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://github.com/kanaka/websockify) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `mox3`, `numpy`, `setuptools`, `urllib3`
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

Backlinks: [[networking]] · [[websockify]] · [[BlackArch Tools]]

