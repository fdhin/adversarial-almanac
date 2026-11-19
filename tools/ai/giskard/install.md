---
tool: "giskard"
build-system: "scripts"
upstream: "https://github.com/Giskard-AI/giskard-oss"
recipe: "blackarch"
source: "https://github.com/Giskard-AI/giskard-oss"
license: "Apache-2.0"
blackarch-pkgver: "3.0.1.r32.g9a30fc3"
---

# Installing giskard

> Evaluation, testing and vulnerability scanning framework for AI agents.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/giskard) (version `3.0.1.r32.g9a30fc3`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S giskard    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Giskard-AI/giskard-oss) (the recipe uses a versioned archive).

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

Backlinks: [[ai]] · [[giskard]] · [[BlackArch Tools]]

