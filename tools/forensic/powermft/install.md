---
tool: "powermft"
build-system: "scripts"
upstream: "https://github.com/jschicht/PowerMft"
recipe: "blackarch"
source: "https://github.com/jschicht/PowerMft"
license: "MIT"
blackarch-pkgver: "5.76574543"
---

# Installing powermft

> Powerful commandline $MFT record editor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/powermft) (version `5.76574543`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S powermft    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/PowerMft.git
cd PowerMft
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `wine`
  - `wine-mono`
  - `wine_gecko`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[forensic]] · [[powermft]] · [[BlackArch Tools]]

