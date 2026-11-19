---
tool: "pyfiscan"
build-system: "scripts"
upstream: "https://github.com/fgeek/pyfiscan"
recipe: "blackarch"
source: "https://github.com/fgeek/pyfiscan"
license: "custom:unknown"
blackarch-pkgver: "3009.358d56a"
---

# Installing pyfiscan

> Free web-application vulnerability and version scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyfiscan) (version `3009.358d56a`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pyfiscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/fgeek/pyfiscan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `chardet`, `docopt`, `jinja`, `pyaml`, `scandir`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[pyfiscan]] · [[BlackArch Tools]]

