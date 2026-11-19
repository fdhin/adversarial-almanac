---
tool: "garak"
build-system: "scripts"
upstream: "https://github.com/NVIDIA/garak"
recipe: "blackarch"
aur-package: "garak"
source: "https://github.com/NVIDIA/garak"
license: "Apache-2.0"
blackarch-pkgver: "0.17.1.r31.g4b7761c"
---

# Installing garak

> The LLM vulnerability scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/garak) (version `0.17.1.r31.g4b7761c`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S garak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/NVIDIA/garak) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[ai]] · [[garak]] · [[BlackArch Tools]]

