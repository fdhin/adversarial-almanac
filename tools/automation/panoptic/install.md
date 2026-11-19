---
tool: "panoptic"
build-system: "scripts"
upstream: "https://github.com/lightos/Panoptic"
recipe: "blackarch"
source: "https://github.com/lightos/Panoptic"
license: "MIT"
blackarch-pkgver: "262.e138360"
---

# Installing panoptic

> A tool that automates the process of search and retrieval of content for common log and config files through LFI vulnerability.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/panoptic) (version `262.e138360`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S panoptic    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lightos/Panoptic.git
cd Panoptic
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[panoptic]] · [[BlackArch Tools]]

