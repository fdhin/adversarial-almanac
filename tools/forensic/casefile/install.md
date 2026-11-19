---
tool: "casefile"
build-system: "scripts"
upstream: "http://www.paterva.com/web6/products/casefile.php"
recipe: "blackarch"
source: "https://github.com/cmlh/Maltego-Entities-Mirror/raw/refs/heads/master/mirror/www/casefile_entities.mtz"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1.0.1"
---

# Installing casefile

> The little brother to Maltego without transforms, but combines graph and link analysis to examine links between manually added data to mind map your information

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/casefile) (version `1.0.1`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S casefile    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/cmlh/Maltego-Entities-Mirror/raw/refs/heads/master/mirror/www/casefile_entities.mtz
cd casefile_entities.mtz
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[casefile]] · [[BlackArch Tools]]

