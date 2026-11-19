---
tool: "openpuff"
build-system: "scripts"
upstream: "https://embeddedsw.net/OpenPuff_Steganography_Home.html"
recipe: "blackarch"
source: "https://embeddedsw.net/zip/OpenPuff_release.zip"
license: "custom:unknown"
blackarch-pkgver: "4.01"
---

# Installing openpuff

> Yet not another steganography SW.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/openpuff) (version `4.01`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S openpuff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://embeddedsw.net/zip/OpenPuff_release.zip
tar xf OpenPuff_release.zip
cd OpenPuff_release
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

Backlinks: [[stego]] · [[openpuff]] · [[BlackArch Tools]]

