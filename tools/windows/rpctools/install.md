---
tool: "rpctools"
build-system: "windows"
upstream: "https://packetstormsecurity.com/files/31879/rpctools-1.0.zip.html"
recipe: "blackarch"
source: "https://packetstormsecurity.com/files/31879/rpctools-1.0.zip.html"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing rpctools

> Contains three separate tools for obtaining information from a system that is running RPC services

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rpctools) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rpctools    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://packetstormsecurity.com/files/31879/rpctools.0.zip.html
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[rpctools]] · [[BlackArch Tools]]

