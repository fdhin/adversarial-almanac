---
tool: "f-scrack"
build-system: "scripts"
upstream: "https://github.com/ysrc/F-Scrack"
recipe: "blackarch"
source: "https://github.com/ysrc/F-Scrack"
license: "GPL3"
blackarch-pkgver: "19.9a00357"
---

# Installing f-scrack

> A single file bruteforcer supports multi-protocol.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/f-scrack) (version `19.9a00357`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S f-scrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ysrc/F-Scrack.git
cd F-Scrack
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[f-scrack]] · [[BlackArch Tools]]

