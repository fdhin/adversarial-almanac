---
tool: "backorifice"
build-system: "windows"
upstream: "http://www.cultdeadcow.com/tools/bo.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/trojans/bo/bo.zip"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing backorifice

> A remote administration system which allows a user to control a computer across a tcpip connection using a simple console or GUI application.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/backorifice) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S backorifice    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.cultdeadcow.com/tools/bo.html
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[backorifice]] · [[BlackArch Tools]]

