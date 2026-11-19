---
tool: "pcapteller"
build-system: "scripts"
upstream: "https://www.encripto.no/nb/downloads/tools/"
recipe: "blackarch"
source: "https://www.encripto.no/nb/downloads/tools/"
license: "BSD"
blackarch-pkgver: "1.1"
---

# Installing pcapteller

> A tool designed for traffic manipulation and replay.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pcapteller) (version `1.1`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S pcapteller    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.encripto.no/nb/downloads/tools/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-ipcalc, python2-scapy, python2-six (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/pcapteller`

Backlinks: [[sniffer]] · [[pcapteller]] · [[BlackArch Tools]]

