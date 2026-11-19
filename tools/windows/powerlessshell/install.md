---
tool: "powerlessshell"
build-system: "windows"
upstream: "https://github.com/Mr-Un1k0d3r/PowerLessShell"
recipe: "blackarch"
source: "https://github.com/Mr-Un1k0d3r/PowerLessShell"
license: "GPL"
blackarch-pkgver: "117.b168ccb"
---

# Installing powerlessshell

> Run PowerShell command without invoking powershell.exe.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/powerlessshell) (version `117.b168ccb`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S powerlessshell    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/Mr-Un1k0d3r/PowerLessShell
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[powerlessshell]] · [[BlackArch Tools]]

