---
tool: "netbus"
build-system: "windows"
upstream: "https://packetstormsecurity.com/files/10320/nb16_p04.zip.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/trojans/nb16_p04.zip"
license: "custom:unknown"
blackarch-pkgver: "1.6"
---

# Installing netbus

> NetBus remote administration tool

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netbus) (version `1.6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S netbus    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://packetstormsecurity.com/files/10320/nb16_p04.zip.html
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[netbus]] · [[BlackArch Tools]]

