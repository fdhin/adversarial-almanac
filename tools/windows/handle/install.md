---
tool: "handle"
build-system: "windows"
upstream: "http://www.tarasco.org/security/handle/index.html"
recipe: "blackarch"
source: "http://www.tarasco.org/security/handle/index.html"
license: "custom"
blackarch-pkgver: "0.1"
---

# Installing handle

> An small application designed to analyze your system searching for global objects related to running process and display information for every found object, like tokens, semaphores, ports, files,..

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/handle) (version `0.1`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S handle    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.tarasco.org/security/handle/index.html
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[handle]] · [[BlackArch Tools]]

