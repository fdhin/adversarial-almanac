---
tool: "instagramosint"
build-system: "scripts"
upstream: "https://github.com/sc1341/InstagramOSINT/"
recipe: "blackarch"
source: "https://github.com/sc1341/InstagramOSINT/"
license: "MIT"
blackarch-pkgver: "21.30b5735"
---

# Installing instagramosint

> An Instagram Open Source Intelligence Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/instagramosint) (version `21.30b5735`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S instagramosint    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/sc1341/InstagramOSINT.git
cd InstagramOSINT
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[instagramosint]] · [[BlackArch Tools]]

