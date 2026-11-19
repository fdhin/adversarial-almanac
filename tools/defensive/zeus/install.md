---
tool: "zeus"
build-system: "scripts"
upstream: "https://github.com/DenizParlak/Zeus"
recipe: "blackarch"
aur-package: "zeus"
source: "https://github.com/DenizParlak/Zeus"
license: "MIT"
blackarch-pkgver: "111.97db152"
---

# Installing zeus

> AWS Auditing & Hardening Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zeus) (version `111.97db152`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S zeus    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/DenizParlak/Zeus.git
cd Zeus
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aws-cli`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[zeus]] · [[BlackArch Tools]]

