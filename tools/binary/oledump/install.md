---
tool: "oledump"
build-system: "scripts"
upstream: "http://blog.didierstevens.com/programs/oledump-py/"
recipe: "blackarch"
source: "http://blog.didierstevens.com/programs/oledump-py/"
license: "custom:unknown"
blackarch-pkgver: "0.0.81"
---

# Installing oledump

> Analyze OLE files (Compound File Binary Format). These files contain streams of data. This tool allows you to analyze these streams.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/oledump) (version `0.0.81`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S oledump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://blog.didierstevens.com/programs/oledump-py/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `lxml`, `olefile`, `yara-python-dex`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[oledump]] · [[BlackArch Tools]]

