---
tool: "ffuf-scripts"
build-system: "scripts"
upstream: "https://github.com/ffuf/ffuf-scripts"
recipe: "blackarch"
source: "https://github.com/ffuf/ffuf-scripts"
license: "custom:unknown"
blackarch-pkgver: "1.2192bf7"
---

# Installing ffuf-scripts

> Scripts and snippets for ffuf payloads.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ffuf-scripts) (version `1.2192bf7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ffuf-scripts    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ffuf/ffuf-scripts) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[ffuf-scripts]] · [[BlackArch Tools]]

