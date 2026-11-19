---
tool: "xerosploit"
build-system: "scripts"
upstream: "https://github.com/LionSec/xerosploit"
recipe: "blackarch"
source: "https://github.com/LionSec/xerosploit"
license: "GPL3"
blackarch-pkgver: "38.e2c3c7b"
---

# Installing xerosploit

> Efficient and advanced man in the middle framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xerosploit) (version `38.e2c3c7b`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S xerosploit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/LionSec/xerosploit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bettercap`
  - `hping`
  - `iproute2`
  - `net-tools`
  - `nmap`
  - `python2`
  - `ruby`
  - `wireless_tools` — Debian wireless-tools / Fedora wireless-tools
- ⚠️ Python 2 only deps: python2-recommonmark, python2-sphinx_rtd_theme, python2-tabulate, python2-terminaltables (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[xerosploit]] · [[BlackArch Tools]]

