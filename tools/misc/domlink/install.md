---
tool: "domlink"
build-system: "scripts"
upstream: "https://github.com/vysecurity/DomLink"
recipe: "blackarch"
source: "https://github.com/vysecurity/DomLink"
license: "MIT"
blackarch-pkgver: "37.1cabd5d"
---

# Installing domlink

> A tool to link a domain with registered organisation names and emails, to other domains.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/domlink) (version `37.1cabd5d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S domlink    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/vysecurity/DomLink.git
cd DomLink
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[domlink]] · [[BlackArch Tools]]

