---
tool: "sprayhound"
build-system: "python"
upstream: "https://github.com/Hackndo/sprayhound"
recipe: "blackarch"
source: "https://github.com/Hackndo/sprayhound"
license: "MIT"
blackarch-pkgver: "0.0.3"
---

# Installing sprayhound

> Password spraying tool and Bloodhound integration.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sprayhound) (version `0.0.3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S sprayhound    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Hackndo/sprayhound) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap`, `neo4j-driver`, `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[sprayhound]] · [[BlackArch Tools]]

