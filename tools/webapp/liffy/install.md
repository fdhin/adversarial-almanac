---
tool: "liffy"
build-system: "scripts"
upstream: "https://github.com/mzfr/liffy/"
recipe: "blackarch"
source: "https://github.com/mzfr/liffy/"
license: "GPL-1.0-or-later"
blackarch-pkgver: "46.b990d6a"
---

# Installing liffy

> A Local File Inclusion Exploitation tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/liffy) (version `46.b990d6a`, license GPL-1.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S liffy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mzfr/liffy/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `daemon`, `pyfiglet`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[liffy]] · [[BlackArch Tools]]

