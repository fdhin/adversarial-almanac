---
tool: "linkedint"
build-system: "scripts"
upstream: "https://github.com/vysecurity/LinkedInt"
recipe: "blackarch"
source: "https://github.com/vysecurity/LinkedInt"
license: "MIT"
blackarch-pkgver: "1.1.FIX.r8.g89ab1d3"
---

# Installing linkedint

> LinkedIn Recon Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/linkedint) (version `1.1.FIX.r8.g89ab1d3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S linkedint    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/vysecurity/LinkedInt.git
cd LinkedInt
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `certifi`, `chardet`, `idna`, `requests`, `thready`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/licneses`

Backlinks: [[social]] · [[linkedint]] · [[BlackArch Tools]]

