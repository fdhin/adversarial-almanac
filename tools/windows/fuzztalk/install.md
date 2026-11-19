---
tool: "fuzztalk"
build-system: "windows"
upstream: "https://code.google.com/p/fuzztalk"
recipe: "blackarch"
source: "https://code.google.com/p/fuzztalk"
license: "custom:unknown"
blackarch-pkgver: "1.0.0.0"
---

# Installing fuzztalk

> An XML driven fuzz testing framework that emphasizes easy extensibility and reusability.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fuzztalk) (version `1.0.0.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S fuzztalk    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://code.google.com/p/fuzztalk
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[fuzztalk]] · [[BlackArch Tools]]

