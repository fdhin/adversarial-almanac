---
tool: "androidmeda"
build-system: "scripts"
upstream: "https://github.com/In3tinct/androidmeda"
recipe: "blackarch"
source: "https://github.com/In3tinct/androidmeda"
license: "Apache-2.0"
blackarch-pkgver: "v2.0.0.r1.ge2e5084"
---

# Installing androidmeda

> AI tool to deobfuscate and find any potential vulnerabilities in android apps.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/androidmeda) (version `v2.0.0.r1.ge2e5084`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S androidmeda    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/In3tinct/androidmeda
cd androidmeda
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[androidmeda]] · [[BlackArch Tools]]

