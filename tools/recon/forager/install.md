---
tool: "forager"
build-system: "scripts"
upstream: "https://github.com/byt3smith/Forager"
recipe: "blackarch"
aur-package: "forager"
source: "https://github.com/byt3smith/Forager"
license: "MIT"
blackarch-pkgver: "v2.0.3.r3.g7439b0a"
---

# Installing forager

> Multithreaded threat Intelligence gathering utilizing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/forager) (version `v2.0.3.r3.g7439b0a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S forager    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/byt3smith/Forager.git
cd Forager
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[forager]] · [[BlackArch Tools]]

