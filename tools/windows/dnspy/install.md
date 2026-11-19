---
tool: "dnspy"
build-system: "windows"
upstream: "https://github.com/0xd4d/dnSpy/"
recipe: "blackarch"
source: "https://github.com/0xd4d/dnSpy/"
license: "GPL3"
blackarch-pkgver: "6.1.8"
---

# Installing dnspy

> .NET debugger and assembly editor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnspy) (version `6.1.8`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dnspy    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/0xd4d/dnSpy/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[dnspy]] · [[BlackArch Tools]]

