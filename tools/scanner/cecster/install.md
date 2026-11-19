---
tool: "cecster"
build-system: "scripts"
upstream: "https://github.com/nccgroup/CECster"
recipe: "blackarch"
source: "https://github.com/nccgroup/CECster"
license: "GPL3"
blackarch-pkgver: "5.15544cb"
---

# Installing cecster

> A tool to perform security testing against the HDMI CEC (Consumer Electronics Control) and HEC (HDMI Ethernet Channel) protocols.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cecster) (version `5.15544cb`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cecster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nccgroup/CECster.git
cd CECster
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `wxpython`
- ⚠️ Python 2 only deps: python2-pyserial, python2-pyusb (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[cecster]] · [[BlackArch Tools]]

