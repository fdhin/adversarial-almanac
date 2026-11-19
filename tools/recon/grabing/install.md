---
tool: "grabing"
build-system: "scripts"
upstream: "https://github.com/black-brain/graBing"
recipe: "blackarch"
source: "https://github.com/black-brain/graBing"
license: "GPL3"
blackarch-pkgver: "11.9c1aa6c"
---

# Installing grabing

> Counts all the hostnames for an IP adress

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/grabing) (version `11.9c1aa6c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S grabing    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/black-brain/graBing.git
cd graBing
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[grabing]] · [[BlackArch Tools]]

