---
tool: "aurebeshjs"
build-system: "scripts"
upstream: "https://github.com/aemkei/aurebesh.js"
recipe: "blackarch"
source: "https://github.com/aemkei/aurebesh.js"
license: "custom:WTFPL"
blackarch-pkgver: "77.75a8fc6"
---

# Installing aurebeshjs

> Translate JavaScript to Other Alphabets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aurebeshjs) (version `77.75a8fc6`, license custom:WTFPL).

## Arch / BlackArch

```bash
sudo pacman -S aurebeshjs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/aemkei/aurebesh.js.git
cd aurebesh.js
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[aurebeshjs]] · [[BlackArch Tools]]

