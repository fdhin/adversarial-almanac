---
tool: "taipan"
build-system: "scripts"
upstream: "https://github.com/enkomio/Taipan"
recipe: "blackarch"
aur-package: "taipan"
source: "https://github.com/enkomio/Taipan"
license: "MIT"
blackarch-pkgver: "2.9.498.18"
---

# Installing taipan

> Web application security scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/taipan) (version `2.9.498.18`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S taipan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/enkomio/Taipan/releases/download/2.7/TaipanScannerCommunity.v2.7.480.30.zip
cd TaipanScannerCommunity.v2.7.480.30.zip
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `mono`
- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[taipan]] · [[BlackArch Tools]]

