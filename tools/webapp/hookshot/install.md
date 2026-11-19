---
tool: "hookshot"
build-system: "scripts"
upstream: "https://github.com/andrew-vii/hookshot/"
recipe: "blackarch"
source: "https://github.com/andrew-vii/hookshot/"
license: "custom:unknown"
blackarch-pkgver: "205.df30b85"
---

# Installing hookshot

> Integrated web scraper and email account data breach comparison tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hookshot) (version `205.df30b85`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hookshot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/andrew-vii/hookshot/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[hookshot]] · [[BlackArch Tools]]

