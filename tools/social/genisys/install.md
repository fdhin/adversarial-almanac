---
tool: "genisys"
build-system: "scripts"
upstream: "https://github.com/ahayder/Genisys"
recipe: "blackarch"
source: "https://github.com/ahayder/Genisys"
license: "custom:unknown"
blackarch-pkgver: "53.d53bb0c"
---

# Installing genisys

> Powerful Telegram Members Scraping and Adding Toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/genisys) (version `53.d53bb0c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S genisys    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ahayder/Genisys.git
cd Genisys
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `keyboard`, `pyfiglet`, `telethon`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[genisys]] · [[BlackArch Tools]]

