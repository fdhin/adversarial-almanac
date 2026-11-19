---
tool: "linkfinder"
build-system: "scripts"
upstream: "https://github.com/GerbenJavado/LinkFinder"
recipe: "blackarch"
source: "https://github.com/GerbenJavado/LinkFinder"
license: "custom:unknown"
blackarch-pkgver: "168.1debac5"
---

# Installing linkfinder

> Discovers endpoint and their parameters in JavaScript files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/linkfinder) (version `168.1debac5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S linkfinder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/GerbenJavado/LinkFinder.git
cd LinkFinder
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `jsbeautifier`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[linkfinder]] · [[BlackArch Tools]]

