---
tool: "google-explorer"
build-system: "scripts"
upstream: "https://github.com/anarcoder/google_explorer"
recipe: "blackarch"
source: "https://github.com/anarcoder/google_explorer"
license: "custom:unknown"
blackarch-pkgver: "140.0b21b57"
---

# Installing google-explorer

> Google mass exploit robot - Make a google search, and parse the results for a especific exploit you define.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/google-explorer) (version `140.0b21b57`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S google-explorer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/anarcoder/google_explorer.git
cd google_explorer
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `docopt`, `lxml`, `requests`, `selenium`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `chromium`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[google-explorer]] · [[BlackArch Tools]]

