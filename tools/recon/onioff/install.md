---
tool: "onioff"
build-system: "scripts"
upstream: "https://github.com/k4m4/onioff"
recipe: "blackarch"
source: "https://github.com/k4m4/onioff"
license: "MIT"
blackarch-pkgver: "84.34dc309"
---

# Installing onioff

> An onion url inspector for inspecting deep web links.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/onioff) (version `84.34dc309`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S onioff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/k4m4/onioff) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `pysocks`, `requests`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[onioff]] · [[BlackArch Tools]]

