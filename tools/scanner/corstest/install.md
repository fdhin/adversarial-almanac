---
tool: "corstest"
build-system: "scripts"
upstream: "https://github.com/RUB-NDS/CORStest"
recipe: "blackarch"
source: "https://github.com/RUB-NDS/CORStest"
license: "GPL-2.0-or-later"
blackarch-pkgver: "10.beffd0b"
---

# Installing corstest

> A simple CORS misconfigurations checker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/corstest) (version `10.beffd0b`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S corstest    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/RUB-NDS/CORStest
cd CORStest
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[corstest]] · [[BlackArch Tools]]

