---
tool: "arpwner"
build-system: "scripts"
upstream: "https://github.com/ntrippar/ARPwner"
recipe: "blackarch"
source: "https://github.com/ntrippar/ARPwner"
license: "GPL-3.0-or-later"
blackarch-pkgver: "26.f300fdf"
---

# Installing arpwner

> GUI-based python tool for arp poisoning and dns poisoning attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/arpwner) (version `26.f300fdf`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S arpwner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ntrippar/ARPwner.git
cd ARPwner
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `pygtk`
  - `python2`
- ⚠️ Python 2 only deps: python2-pypcap (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[arpwner]] · [[BlackArch Tools]]

