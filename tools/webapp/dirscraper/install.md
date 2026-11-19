---
tool: "dirscraper"
build-system: "scripts"
upstream: "https://github.com/Cillian-Collins/dirscraper"
recipe: "blackarch"
source: "https://github.com/Cillian-Collins/dirscraper"
license: "custom:unknown"
blackarch-pkgver: "16.e752450"
---

# Installing dirscraper

> OSINT Scanning tool which discovers and maps directories found in javascript files hosted on a website.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dirscraper) (version `16.e752450`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dirscraper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Cillian-Collins/dirscraper) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `html5lib`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[dirscraper]] · [[BlackArch Tools]]

