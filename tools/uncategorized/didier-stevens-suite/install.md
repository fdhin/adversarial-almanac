---
tool: "didier-stevens-suite"
build-system: "scripts"
upstream: "https://github.com/DidierStevens/DidierStevensSuite"
recipe: "blackarch"
source: "https://github.com/DidierStevens/DidierStevensSuite"
license: "unknown"
blackarch-pkgver: "448.7d625d0"
---

# Installing didier-stevens-suite

> Didier Stevens Suite.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/didier-stevens-suite) (version `448.7d625d0`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S didier-stevens-suite    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/DidierStevens/DidierStevensSuite.git
cd DidierStevensSuite
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[uncategorized]] · [[didier-stevens-suite]] · [[BlackArch Tools]]

