---
tool: "identywaf"
build-system: "scripts"
upstream: "https://github.com/stamparm/identYwaf"
recipe: "blackarch"
source: "https://github.com/stamparm/identYwaf"
license: "MIT"
blackarch-pkgver: "208.3e535b7"
---

# Installing identywaf

> Blind WAF identification tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/identywaf) (version `208.3e535b7`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S identywaf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/stamparm/identYwaf.git
cd identYwaf
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[identywaf]] · [[BlackArch Tools]]

