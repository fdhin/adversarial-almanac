---
tool: "frisbeelite"
build-system: "scripts"
upstream: "https://github.com/nccgroup/FrisbeeLite"
recipe: "blackarch"
source: "https://github.com/nccgroup/FrisbeeLite"
license: "GPL3"
blackarch-pkgver: "1.2"
---

# Installing frisbeelite

> A GUI-based USB device fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/frisbeelite) (version `1.2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S frisbeelite    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nccgroup/FrisbeeLite.git
cd FrisbeeLite
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel
  - `python2`
  - `wxwidgets-gtk3`
- ⚠️ Python 2 only deps: python2-pyusb, python2-wxpython3 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[frisbeelite]] · [[BlackArch Tools]]

