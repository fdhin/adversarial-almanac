---
tool: "httpbog"
build-system: "windows"
upstream: "http://sourceforge.net/projects/httpbog/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/httpbog/"
license: "custom:unknown"
blackarch-pkgver: "1.0.0.0"
---

# Installing httpbog

> A slow HTTP denial-of-service tool that works similarly to other attacks, but rather than leveraging request headers or POST data Bog consumes sockets by slowly reading responses.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httpbog) (version `1.0.0.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S httpbog    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://sourceforge.net/projects/httpbog/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[httpbog]] · [[BlackArch Tools]]

