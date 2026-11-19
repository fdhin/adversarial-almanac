---
tool: "rdpassspray"
build-system: "scripts"
upstream: "https://github.com/xFreed0m/RDPassSpray"
recipe: "blackarch"
source: "https://github.com/xFreed0m/RDPassSpray"
license: "GPL-3.0-or-later"
blackarch-pkgver: "33.c1ba58e"
---

# Installing rdpassspray

> Python3 tool to perform password spraying using RDP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rdpassspray) (version `33.c1ba58e`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S rdpassspray    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/xFreed0m/RDPassSpray.git
cd RDPassSpray
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorlog`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `freerdp`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[rdpassspray]] · [[BlackArch Tools]]

