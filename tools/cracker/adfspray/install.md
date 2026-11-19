---
tool: "adfspray"
build-system: "scripts"
upstream: "https://github.com/xFreed0m/ADFSpray"
recipe: "blackarch"
source: "https://github.com/xFreed0m/ADFSpray"
license: "GPL-3.0-or-later"
blackarch-pkgver: "6.3d7745d"
---

# Installing adfspray

> Python3 tool to perform password spraying against Microsoft Online service using various methods.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/adfspray) (version `6.3d7745d`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S adfspray    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/xFreed0m/ADFSpray.git
cd ADFSpray
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorlog`, `requests`, `requests-ntlm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[adfspray]] · [[BlackArch Tools]]

