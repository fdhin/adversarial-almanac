---
tool: "crozono"
build-system: "scripts"
upstream: "https://github.com/crozono/crozono-free"
recipe: "blackarch"
source: "https://github.com/crozono/crozono-free"
license: "custom:unknown"
blackarch-pkgver: "5.6a51669"
---

# Installing crozono

> A modular framework designed to automate the penetration testing of wireless networks from drones and such unconventional devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crozono) (version `5.6a51669`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S crozono    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/andy737/crozono-free.git
cd crozono-free
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `pexpect`, `poormanslogging`, `pyfiglet`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[drone]] · [[crozono]] · [[BlackArch Tools]]

