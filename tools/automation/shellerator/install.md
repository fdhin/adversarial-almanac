---
tool: "shellerator"
build-system: "python"
upstream: "https://github.com/ShutdownRepo/shellerator"
recipe: "blackarch"
source: "https://github.com/ShutdownRepo/shellerator"
license: "GPL-3.0-or-later"
blackarch-pkgver: "72.6af7f36"
---

# Installing shellerator

> Simple command-line tool aimed to help pentesters quickly generate one-liner reverse/bind shells in multiple languages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shellerator) (version `72.6af7f36`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S shellerator    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ShutdownRepo/shellerator) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `colorama`, `console-menu`, `hatchling`, `installer`, `psutil`, `setuptools`, `simple-term-menu`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[shellerator]] · [[BlackArch Tools]]

