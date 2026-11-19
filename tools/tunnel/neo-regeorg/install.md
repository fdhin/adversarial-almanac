---
tool: "neo-regeorg"
build-system: "scripts"
upstream: "https://github.com/L-codes/Neo-reGeorg"
recipe: "blackarch"
source: "https://github.com/L-codes/Neo-reGeorg"
license: "GPL3"
blackarch-pkgver: "v5.3.0.r3.g5357264"
---

# Installing neo-regeorg

> Improved version of reGeorg, HTTP tunneling pivot tool

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/neo-regeorg) (version `v5.3.0.r3.g5357264`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S neo-regeorg    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/L-codes/Neo-reGeorg.git
cd Neo-reGeorg
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[neo-regeorg]] · [[BlackArch Tools]]

