---
tool: "bridgekeeper"
build-system: "scripts"
upstream: "https://github.com/0xZDH/BridgeKeeper"
recipe: "blackarch"
source: "https://github.com/0xZDH/BridgeKeeper"
license: "custom:unknown"
blackarch-pkgver: "57.55c390c"
---

# Installing bridgekeeper

> Scrape employee names from search engine LinkedIn profiles. Convert employee names to a specified username format.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bridgekeeper) (version `57.55c390c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S bridgekeeper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/0xZDH/BridgeKeeper.git
cd BridgeKeeper
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `lxml`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[bridgekeeper]] · [[BlackArch Tools]]

