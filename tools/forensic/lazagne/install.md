---
tool: "lazagne"
build-system: "scripts"
upstream: "https://github.com/AlessandroZ/LaZagne"
recipe: "blackarch"
aur-package: "lazagne"
source: "https://github.com/AlessandroZ/LaZagne"
license: "LGPL-3.0-or-later"
blackarch-pkgver: "v2.4.7.r6.g858ff6f"
---

# Installing lazagne

> An open source application used to retrieve lots of passwords stored on a local computer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lazagne) (version `v2.4.7.r6.g858ff6f`, license LGPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S lazagne    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/AlessandroZ/LaZagne.git
cd LaZagne
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `libkeepass`, `pbkdf2`, `psutil`, `pyasn1`, `pycryptodome`, `pydes`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[lazagne]] · [[BlackArch Tools]]

