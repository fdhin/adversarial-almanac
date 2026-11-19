---
tool: "lhf"
build-system: "scripts"
upstream: "https://github.com/blindfuzzy/LHF"
recipe: "blackarch"
source: "https://github.com/blindfuzzy/LHF"
license: "GPL3"
blackarch-pkgver: "40.51568ee"
---

# Installing lhf

> A modular recon tool for pentesting.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lhf) (version `40.51568ee`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S lhf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/blindfuzzy/LHF.git
cd LHF
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `arachni`
  - `dirb`
  - `enum4linux`
  - `hydra`
  - `nikto`
  - `nmap`
  - `python2`
  - `sqlmap`
- ⚠️ Python 2 only deps: python2-psutil, python2-python-libnmap, python2-selenium (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[lhf]] · [[BlackArch Tools]]

