---
tool: "mxtract"
build-system: "scripts"
upstream: "https://github.com/rek7/mXtract"
recipe: "blackarch"
source: "https://github.com/rek7/mXtract"
license: "MIT"
blackarch-pkgver: "90.0b34376"
---

# Installing mxtract

> Memory Extractor & Analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mxtract) (version `90.0b34376`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S mxtract    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/rek7/mXtract.git
cd mXtract
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[mxtract]] · [[BlackArch Tools]]

