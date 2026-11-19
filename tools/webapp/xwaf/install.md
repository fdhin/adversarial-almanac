---
tool: "xwaf"
build-system: "scripts"
upstream: "https://github.com/3xp10it/bypass_waf"
recipe: "blackarch"
source: "https://github.com/3xp10it/bypass_waf"
license: "custom:unknown"
blackarch-pkgver: "162.c6f6bb7"
---

# Installing xwaf

> Automatic WAF bypass tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xwaf) (version `162.c6f6bb7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S xwaf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/3xp10it/bypass_waf.git
cd bypass_waf
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xwaf]] · [[BlackArch Tools]]

