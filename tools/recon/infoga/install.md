---
tool: "infoga"
build-system: "scripts"
upstream: "https://github.com/m4ll0k/infoga"
recipe: "blackarch"
source: "https://github.com/m4ll0k/infoga"
license: "MIT"
blackarch-pkgver: "33.79a1c03"
---

# Installing infoga

> Tool for gathering e-mail accounts information from different public sources (search engines, pgp key servers).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/infoga) (version `33.79a1c03`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S infoga    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/m4ll0k/infoga) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[infoga]] · [[BlackArch Tools]]

