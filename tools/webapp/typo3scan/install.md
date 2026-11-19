---
tool: "typo3scan"
build-system: "scripts"
upstream: "https://github.com/whoot/Typo3Scan"
recipe: "blackarch"
source: "https://github.com/whoot/Typo3Scan"
license: "GPL-2.0-or-later"
blackarch-pkgver: "v1.2.final.r0.g0c4ec73"
---

# Installing typo3scan

> Enumerate Typo3 version and extensions.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/typo3scan) (version `v1.2.final.r0.g0c4ec73`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S typo3scan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/whoot/Typo3Scan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `fake-useragent`, `progressbar`, `requests`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[typo3scan]] · [[BlackArch Tools]]

