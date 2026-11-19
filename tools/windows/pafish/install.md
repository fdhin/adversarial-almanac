---
tool: "pafish"
build-system: "windows"
upstream: "http://www.hoobie.net/pafish/"
recipe: "blackarch"
source: "http://www.hoobie.net/pafish/"
license: "GPL3"
blackarch-pkgver: "193.b497899"
---

# Installing pafish

> A demonstration tool that employs several techniques to detect sandboxes and analysis environments in the same way as malware families do.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pafish) (version `193.b497899`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pafish    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.hoobie.net/pafish/
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[pafish]] · [[BlackArch Tools]]

