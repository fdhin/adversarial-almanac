---
tool: "spookflare"
build-system: "scripts"
upstream: "https://github.com/hlldz/SpookFlare"
recipe: "blackarch"
source: "https://github.com/hlldz/SpookFlare"
license: "Apache"
blackarch-pkgver: "24.19491b5"
---

# Installing spookflare

> Loader, dropper generator with multiple features for bypassing client-side and network-side countermeasures.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spookflare) (version `24.19491b5`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S spookflare    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/hlldz/SpookFlare.git
cd SpookFlare
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `terminaltables`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[spookflare]] · [[BlackArch Tools]]

