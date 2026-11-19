---
tool: "analyzepesig"
build-system: "windows"
upstream: "https://blog.didierstevens.com/my-software/#AnalyzePESig"
recipe: "blackarch"
source: "https://blog.didierstevens.com/my-software/#AnalyzePESig"
license: "custom:unknown"
blackarch-pkgver: "0.0.0.5"
---

# Installing analyzepesig

> Analyze digital signature of PE file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/analyzepesig) (version `0.0.0.5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S analyzepesig    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://blog.didierstevens.com/my-software/#AnalyzePESig
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pefile (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[analyzepesig]] · [[BlackArch Tools]]

