---
tool: "juicy-potato"
build-system: "windows"
upstream: "https://github.com/ohpe/juicy-potato"
recipe: "blackarch"
source: "https://github.com/ohpe/juicy-potato"
license: "GPL3"
blackarch-pkgver: "53.744d321"
---

# Installing juicy-potato

> A sugared version of RottenPotatoNG, with a bit of juice.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/juicy-potato) (version `53.744d321`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S juicy-potato    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/ohpe/juicy-potato
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[juicy-potato]] · [[BlackArch Tools]]

