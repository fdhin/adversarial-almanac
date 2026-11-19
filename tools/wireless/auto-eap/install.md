---
tool: "auto-eap"
build-system: "scripts"
upstream: "https://github.com/Tylous/Auto_EAP"
recipe: "blackarch"
source: "https://github.com/Tylous/Auto_EAP"
license: "GPL2"
blackarch-pkgver: "18.ee36d37"
---

# Installing auto-eap

> Automated Brute-Force Login Attacks Against EAP Networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/auto-eap) (version `18.ee36d37`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S auto-eap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Tylous/Auto_EAP.git
cd Auto_EAP
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `wpa_supplicant`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[auto-eap]] · [[BlackArch Tools]]

