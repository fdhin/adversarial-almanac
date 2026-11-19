---
tool: "extractbitlockerkeys"
build-system: "windows"
upstream: "https://github.com/p0dalirius/extractbitlockerkeys"
recipe: "blackarch"
source: "https://github.com/p0dalirius/extractbitlockerkeys"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1.2.r20.g54730e4"
---

# Installing extractbitlockerkeys

> Script to automatically extract the bitlocker recovery keys from a domain.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/extractbitlockerkeys) (version `1.2.r20.g54730e4`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S extractbitlockerkeys    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/p0dalirius/extractbitlockerkeys
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `sectools`, `xlsxwriter`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[extractbitlockerkeys]] · [[BlackArch Tools]]

