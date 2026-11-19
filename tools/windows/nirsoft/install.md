---
tool: "nirsoft"
build-system: "windows"
upstream: "https://www.nirsoft.net"
recipe: "blackarch"
source: "https://www.nirsoft.net"
license: "unknown"
blackarch-pkgver: "1.30.23"
---

# Installing nirsoft

> Unique collection of small and useful freeware utilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nirsoft) (version `1.30.23`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S nirsoft    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://www.nirsoft.net
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[nirsoft]] · [[BlackArch Tools]]

