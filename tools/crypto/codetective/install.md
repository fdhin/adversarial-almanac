---
tool: "codetective"
build-system: "scripts"
upstream: "https://www.digitalloft.org/init/plugin_wiki/page/codetective"
recipe: "blackarch"
source: "https://www.digitalloft.org/init/plugin_wiki/page/codetective"
license: "custom:unknown"
blackarch-pkgver: "45.52b91f1"
---

# Installing codetective

> A tool to determine the crypto/encoding algorithm used according to traces of its representation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/codetective) (version `45.52b91f1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S codetective    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone git://github.com/blackthorne/Codetective.git
cd Codetective
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[codetective]] · [[BlackArch Tools]]

