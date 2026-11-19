---
tool: "filefuzz"
build-system: "windows"
upstream: "http://www.fuzzing.org/"
recipe: "blackarch"
source: "http://www.fuzzing.org/wp-content/FileFuzz.zip"
license: "GPL2"
blackarch-pkgver: "1.0"
---

# Installing filefuzz

> A binary file fuzzer for Windows with several options.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/filefuzz) (version `1.0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S filefuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.fuzzing.org/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[filefuzz]] · [[BlackArch Tools]]

