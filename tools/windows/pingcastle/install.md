---
tool: "pingcastle"
build-system: "windows"
upstream: "https://www.pingcastle.com"
recipe: "blackarch"
source: "https://www.pingcastle.com"
license: "unknown"
blackarch-pkgver: "3.4.1.38"
---

# Installing pingcastle

> Active Directory scanning tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pingcastle) (version `3.4.1.38`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S pingcastle    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://www.pingcastle.com
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[pingcastle]] · [[BlackArch Tools]]

