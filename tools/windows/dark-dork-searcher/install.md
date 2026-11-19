---
tool: "dark-dork-searcher"
build-system: "windows"
upstream: "http://rafale.org/~mattoufoutu/darkc0de.com/c0de/c/"
recipe: "blackarch"
source: "http://rafale.org/~mattoufoutu/darkc0de.com/c0de/c/"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing dark-dork-searcher

> Dark-Dork Searcher.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dark-dork-searcher) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dark-dork-searcher    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://rafale.org/~mattoufoutu/darkc0de.com/c0de/c/
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unrar`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[dark-dork-searcher]] · [[BlackArch Tools]]

