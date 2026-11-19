---
tool: "keye"
build-system: "scripts"
upstream: "https://github.com/clirimemini/Keye"
recipe: "blackarch"
source: "https://github.com/clirimemini/Keye"
license: "MIT"
blackarch-pkgver: "29.d44a578"
---

# Installing keye

> Recon tool detecting changes of websites based on content-length differences.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/keye) (version `29.d44a578`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S keye    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/clirimemini/Keye.git
cd Keye
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[keye]] · [[BlackArch Tools]]

