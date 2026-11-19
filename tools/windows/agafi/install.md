---
tool: "agafi"
build-system: "windows"
upstream: "https://github.com/CoreSecurity/Agafi"
recipe: "blackarch"
source: "https://github.com/CoreSecurity/Agafi"
license: "custom:unknown"
blackarch-pkgver: "1.1"
---

# Installing agafi

> A gadget finder and a ROP-Chainer tool for x86 platforms.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/agafi) (version `1.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S agafi    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/CoreSecurity/Agafi
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unrar`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[agafi]] · [[BlackArch Tools]]

