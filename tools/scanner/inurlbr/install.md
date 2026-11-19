---
tool: "inurlbr"
build-system: "scripts"
upstream: "https://code.google.com/p/inurlbr/"
recipe: "blackarch"
source: "https://code.google.com/p/inurlbr/"
license: "custom:unknown"
blackarch-pkgver: "34.dbf9773"
---

# Installing inurlbr

> Advanced search in the search engines - Inurl scanner, dorker, exploiter.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/inurlbr) (version `34.dbf9773`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S inurlbr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/googleinurl/SCANNER-INURLBR.git
cd SCANNER-INURLBR
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[inurlbr]] · [[BlackArch Tools]]

