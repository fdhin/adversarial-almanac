---
tool: "rsatool"
build-system: "scripts"
upstream: "https://github.com/ius/rsatool"
recipe: "blackarch"
source: "https://github.com/ius/rsatool"
license: "BSD"
blackarch-pkgver: "34.b34862a"
---

# Installing rsatool

> Tool that can be used to calculate RSA and RSA-CRT parameters.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rsatool) (version `34.b34862a`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S rsatool    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ius/rsatool) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `gmpy2`, `pyasn1`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[rsatool]] · [[BlackArch Tools]]

