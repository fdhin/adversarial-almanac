---
tool: "netripper"
build-system: "windows"
upstream: "https://github.com/NytroRST/NetRipper"
recipe: "blackarch"
source: "https://github.com/NytroRST/NetRipper"
license: "GPL3"
blackarch-pkgver: "84.c763bd0"
---

# Installing netripper

> Smart traffic sniffing for penetration testers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netripper) (version `84.c763bd0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S netripper    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/NytroRST/NetRipper
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `sed`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[netripper]] · [[BlackArch Tools]]

