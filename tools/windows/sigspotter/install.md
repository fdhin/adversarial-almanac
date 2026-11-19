---
tool: "sigspotter"
build-system: "windows"
upstream: "http://www.security-projects.com/?SigSpotter"
recipe: "blackarch"
source: "http://www.security-projects.com/?SigSpotter"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing sigspotter

> A tool that search in your HD to find which publishers has been signed binaries in your PC.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sigspotter) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sigspotter    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.security-projects.com/?SigSpotter
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unrar`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[sigspotter]] · [[BlackArch Tools]]

