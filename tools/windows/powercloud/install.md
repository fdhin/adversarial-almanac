---
tool: "powercloud"
build-system: "windows"
upstream: "https://github.com/mantvydasb/Invoke-PowerCloud"
recipe: "blackarch"
source: "https://github.com/mantvydasb/Invoke-PowerCloud"
license: "custom:unknown"
blackarch-pkgver: "21.0928303"
---

# Installing powercloud

> Deliver powershell payloads via DNS TXT via CloudFlare using PowerShell.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/powercloud) (version `21.0928303`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S powercloud    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/mantvydasb/Invoke-PowerCloud
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[powercloud]] · [[BlackArch Tools]]

