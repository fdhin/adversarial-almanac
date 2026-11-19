---
tool: "shadowexplorer"
build-system: "scripts"
upstream: "https://www.shadowexplorer.com/downloads.html"
recipe: "blackarch"
source: "https://www.shadowexplorer.com/uploads/ShadowExplorer-0.9-portable.zip"
license: "custom:unknown"
blackarch-pkgver: "0.9"
---

# Installing shadowexplorer

> Browse the Shadow Copies created by the Windows Vista / 7 / 8 / 10 Volume Shadow Copy Service.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shadowexplorer) (version `0.9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S shadowexplorer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://www.shadowexplorer.com/uploads/ShadowExplorer-0.9-portable.zip
tar xf ShadowExplorer-0.9-portable.zip
cd ShadowExplorer-0.9-portable
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `wine`
  - `wine-mono`
  - `wine_gecko`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[forensic]] · [[shadowexplorer]] · [[BlackArch Tools]]

