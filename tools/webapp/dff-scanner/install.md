---
tool: "dff-scanner"
build-system: "scripts"
upstream: "https://www.security-net.biz/dff-default-files-and-folders-scanner-p77.html"
recipe: "blackarch"
source: "https://security-net.biz/files/dff/DFF.zip"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.1"
---

# Installing dff-scanner

> Tool for finding path of predictable resource locations.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dff-scanner) (version `1.1`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S dff-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://security-net.biz/files/dff/DFF.zip
tar xf DFF.zip
cd DFF
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[dff-scanner]] · [[BlackArch Tools]]

