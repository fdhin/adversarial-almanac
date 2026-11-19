---
tool: "hookanalyser"
build-system: "windows"
upstream: "http://hookanalyser.blogspot.de/"
recipe: "blackarch"
source: "http://hookanalyser.blogspot.de/"
license: "custom"
blackarch-pkgver: "3.4"
---

# Installing hookanalyser

> A hook tool which can be potentially helpful in reversing applications and analyzing malware. It can hook to an API in a process and search for a pattern in memory or dump the buffer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hookanalyser) (version `3.4`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S hookanalyser    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://hookanalyser.blogspot.de/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[hookanalyser]] · [[BlackArch Tools]]

