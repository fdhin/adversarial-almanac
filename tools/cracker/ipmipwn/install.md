---
tool: "ipmipwn"
build-system: "scripts"
upstream: "https://github.com/AnarchyAngel/IPMIPWN"
recipe: "blackarch"
source: "https://github.com/AnarchyAngel/IPMIPWN"
license: "custom:unknown"
blackarch-pkgver: "6.74a08a8"
---

# Installing ipmipwn

> IPMI cipher 0 attack tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ipmipwn) (version `6.74a08a8`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ipmipwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/AnarchyAngel/IPMIPWN.git
cd IPMIPWN
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ipmitool`
  - `nmap`
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[ipmipwn]] · [[BlackArch Tools]]

