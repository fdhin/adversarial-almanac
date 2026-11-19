---
tool: "sticky-keys-hunter"
build-system: "scripts"
upstream: "https://github.com/ztgrace/sticky_keys_hunter"
recipe: "blackarch"
source: "https://github.com/ztgrace/sticky_keys_hunter"
license: "GPL3"
blackarch-pkgver: "15.c816fc9"
---

# Installing sticky-keys-hunter

> Script to test an RDP host for sticky keys and utilman backdoor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sticky-keys-hunter) (version `15.c816fc9`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sticky-keys-hunter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ztgrace/sticky_keys_hunter.git
cd sticky_keys_hunter
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bc`
  - `imagemagick` — Debian imagemagick / Fedora ImageMagick
  - `rdesktop`
  - `xdotool`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[sticky-keys-hunter]] · [[BlackArch Tools]]

