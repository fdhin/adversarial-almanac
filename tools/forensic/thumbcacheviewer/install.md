---
tool: "thumbcacheviewer"
build-system: "scripts"
upstream: "https://github.com/thumbcacheviewer/thumbcacheviewer"
recipe: "blackarch"
source: "https://github.com/thumbcacheviewer/thumbcacheviewer"
license: "custom:unknown"
blackarch-pkgver: "1.0.3.7"
---

# Installing thumbcacheviewer

> Extract Windows thumbcache database files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/thumbcacheviewer) (version `1.0.3.7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S thumbcacheviewer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/thumbcacheviewer/thumbcacheviewer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `wine`
  - `wine-mono`
  - `wine_gecko`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[forensic]] · [[thumbcacheviewer]] · [[BlackArch Tools]]

