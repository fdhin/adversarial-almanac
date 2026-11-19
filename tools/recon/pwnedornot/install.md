---
tool: "pwnedornot"
build-system: "scripts"
upstream: "https://github.com/thewhiteh4t/pwnedOrNot"
recipe: "blackarch"
source: "https://github.com/thewhiteh4t/pwnedOrNot"
license: "MIT"
blackarch-pkgver: "152.c388d26"
---

# Installing pwnedornot

> Tool to find passwords for compromised email addresses.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwnedornot) (version `152.c388d26`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pwnedornot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/thewhiteh4t/pwnedOrNot.git
cd pwnedOrNot
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[pwnedornot]] · [[BlackArch Tools]]

