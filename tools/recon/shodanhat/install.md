---
tool: "shodanhat"
build-system: "scripts"
upstream: "https://github.com/HatBashBR/ShodanHat"
recipe: "blackarch"
source: "https://github.com/HatBashBR/ShodanHat"
license: "MIT"
blackarch-pkgver: "13.e5e7e68"
---

# Installing shodanhat

> Search for hosts info with shodan.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shodanhat) (version `13.e5e7e68`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S shodanhat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/HatBashBR/ShodanHat.git
cd ShodanHat
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-python-nmap, python2-shodan (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[shodanhat]] · [[BlackArch Tools]]

