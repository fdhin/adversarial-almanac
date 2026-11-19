---
tool: "cachedump"
build-system: "windows"
upstream: "https://packetstormsecurity.com/files/36781/cachedump-1.1.zip.html"
recipe: "blackarch"
source: "https://packetstormsecurity.com/files/36781/cachedump-1.1.zip.html"
license: "custom:unknown"
blackarch-pkgver: "1.1"
---

# Installing cachedump

> A tool that demonstrates how to recover cache entry information: username and hashed password (called MSCASH).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cachedump) (version `1.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S cachedump    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://packetstormsecurity.com/files/36781/cachedump.1.zip.html
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[cachedump]] · [[BlackArch Tools]]

