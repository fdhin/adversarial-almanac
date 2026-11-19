---
tool: "de4dotex"
build-system: "windows"
upstream: "https://github.com/GDATAAdvancedAnalytics/de4dotEx"
recipe: "blackarch"
source: "https://github.com/GDATAAdvancedAnalytics/de4dotEx"
license: "GPL-3.0-or-later"
blackarch-pkgver: "3.12.0.r0.gee602ca"
---

# Installing de4dotex

> .NET deobfuscator and unpacker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/de4dotex) (version `3.12.0.r0.gee602ca`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S de4dotex    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/GDATAAdvancedAnalytics/de4dotEx
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `dotnet-sdk`, `git`, `mono`
- **System libraries** (Arch name — translate for your distro):
  - `mono-msbuild`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[de4dotex]] · [[BlackArch Tools]]

