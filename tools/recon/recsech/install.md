---
tool: "recsech"
build-system: "scripts"
upstream: "https://github.com/radenvodka/Recsech"
recipe: "blackarch"
source: "https://github.com/radenvodka/Recsech"
license: "GPL3"
blackarch-pkgver: "123.1fc298a"
---

# Installing recsech

> Tool for doing Footprinting and Reconnaissance on the target web.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/recsech) (version `123.1fc298a`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S recsech    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/radenvodka/Recsech.git
cd Recsech
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[recsech]] · [[BlackArch Tools]]

