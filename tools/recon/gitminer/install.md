---
tool: "gitminer"
build-system: "scripts"
upstream: "https://github.com/UnkL4b/Gitminer3"
recipe: "blackarch"
source: "https://github.com/UnkL4b/Gitminer3"
license: "GPL-3.0-or-later"
blackarch-pkgver: "8.071385f"
---

# Installing gitminer

> Tool for advanced mining of sensitive content on Github.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gitminer) (version `8.071385f`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S gitminer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/UnkL4b/Gitminer3.git
cd Gitminer3
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `requests`, `tqdm`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[gitminer]] · [[BlackArch Tools]]

