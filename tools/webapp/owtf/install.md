---
tool: "owtf"
build-system: "scripts"
upstream: "https://www.owasp.org/index.php/OWASP_OWTF"
recipe: "blackarch"
source: "https://www.owasp.org/index.php/OWASP_OWTF"
license: "custom"
blackarch-pkgver: "v2.7.0.r0.g2c1c29d"
---

# Installing owtf

> The Offensive (Web) Testing Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/owtf) (version `v2.7.0.r0.g2c1c29d`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S owtf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.owasp.org/index.php/OWASP_OWTF) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pyaml`, `virtualenv`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `postgresql`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[owtf]] · [[BlackArch Tools]]

