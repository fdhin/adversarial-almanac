---
tool: "bashscan"
build-system: "scripts"
upstream: "https://github.com/astryzia/BashScan"
recipe: "blackarch"
source: "https://github.com/astryzia/BashScan"
license: "MIT"
blackarch-pkgver: "94.80c066c"
---

# Installing bashscan

> A port scanner built to utilize /dev/tcp for network and service discovery.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bashscan) (version `94.80c066c`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S bashscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/astryzia/BashScan.git
cd BashScan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bc`
  - `bind`
  - `curl`
  - `net-tools`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[bashscan]] · [[BlackArch Tools]]

