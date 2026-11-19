---
tool: "screamer"
build-system: "python"
upstream: "https://github.com/ifritnoises/screamer"
recipe: "blackarch"
aur-package: "screamer"
source: "https://github.com/ifritnoises/screamer"
license: "MIT"
blackarch-pkgver: "0.2.0"
---

# Installing screamer

> Fast Subnet Discovery.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/screamer) (version `0.2.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S screamer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ifritnoises/screamer) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `colorama`, `installer`, `scapy`, `setuptools`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[screamer]] · [[BlackArch Tools]]

