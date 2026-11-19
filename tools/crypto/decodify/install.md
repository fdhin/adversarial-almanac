---
tool: "decodify"
build-system: "scripts"
upstream: "https://github.com/UltimateHackers/Decodify"
recipe: "blackarch"
source: "https://github.com/UltimateHackers/Decodify"
license: "GPL3"
blackarch-pkgver: "63.4f82883"
---

# Installing decodify

> Tool that can detect and decode encoded strings, recursively.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/decodify) (version `63.4f82883`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S decodify    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/UltimateHackers/Decodify.git
cd Decodify
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `future`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[decodify]] · [[BlackArch Tools]]

