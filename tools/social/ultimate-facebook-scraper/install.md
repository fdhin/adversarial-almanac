---
tool: "ultimate-facebook-scraper"
build-system: "scripts"
upstream: "https://github.com/harismuneer/Ultimate-Facebook-Scraper"
recipe: "blackarch"
source: "https://github.com/harismuneer/Ultimate-Facebook-Scraper"
license: "MIT"
blackarch-pkgver: "323.16f9a15"
provides: ["ufb-scraper"]
---

# Installing ultimate-facebook-scraper

> A bot which scrapes almost everything about a Facebook user's profile.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ultimate-facebook-scraper) (version `323.16f9a15`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ultimate-facebook-scraper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/harismuneer/Ultimate-Facebook-Scraper.git
cd Ultimate-Facebook-Scraper
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `selenium`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `chromium`
  - `python`

## Installed layout (per recipe)

- Commands: `/usr/bin/ufb-scraper`

Backlinks: [[social]] · [[ultimate-facebook-scraper]] · [[BlackArch Tools]]

