---
tool: "antiransom"
build-system: "windows"
upstream: "http://www.security-projects.com/?Anti_Ransom___Download"
recipe: "blackarch"
source: "http://www.security-projects.com/?Anti_Ransom___Download"
license: "custom:unknown"
blackarch-pkgver: "5"
---

# Installing antiransom

> A tool capable of detect and stop attacks of Ransomware using honeypots.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/antiransom) (version `5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S antiransom    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.security-projects.com/?Anti_Ransom___Download
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[antiransom]] · [[BlackArch Tools]]

