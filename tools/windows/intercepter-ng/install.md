---
tool: "intercepter-ng"
build-system: "windows"
upstream: "http://sniff.su/download.html"
recipe: "blackarch"
source: "http://sniff.su/download.html"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing intercepter-ng

> A next generation sniffer including a lot of features: capturing passwords/hashes, sniffing chat messages, performing man-in-the-middle attacks, etc.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/intercepter-ng) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S intercepter-ng    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://sniff.su/download.html
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[intercepter-ng]] · [[BlackArch Tools]]

