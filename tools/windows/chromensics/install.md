---
tool: "chromensics"
build-system: "windows"
upstream: "https://sourceforge.net/projects/chromensics/"
recipe: "blackarch"
source: "https://sourceforge.net/projects/chromensics/"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing chromensics

> A Google chrome forensics tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chromensics) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S chromensics    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://sourceforge.net/projects/chromensics/
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[chromensics]] · [[BlackArch Tools]]

