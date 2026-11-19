---
tool: "dragonrepo"
build-system: "python"
upstream: "https://github.com/12hrformat/dragonrepo"
recipe: "blackarch"
source: "https://github.com/12hrformat/dragonrepo"
license: "MIT"
blackarch-pkgver: "1.0.2.r3.g580356b"
---

# Installing dragonrepo

> Automated penetration testing report generator with command tracking and evidence.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dragonrepo) (version `1.0.2.r3.g580356b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dragonrepo    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/12hrformat/dragonrepo) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `installer`, `jinja`, `markdown`, `rich`, `setuptools`, `typer`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $_pkgname

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[dragonrepo]] · [[BlackArch Tools]]

