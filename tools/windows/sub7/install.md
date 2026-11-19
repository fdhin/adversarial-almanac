---
tool: "sub7"
build-system: "windows"
upstream: "https://dl.packetstormsecurity.net/trojans/Subseven.2.2.zip"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/trojans/Subseven.2.2.zip"
license: "custom:unknown"
blackarch-pkgver: "2.2"
---

# Installing sub7

> A remote administration tool. No further comments ;-)

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sub7) (version `2.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sub7    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://dl.packetstormsecurity.net/trojans/Subseven.2.2.zip
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[sub7]] · [[BlackArch Tools]]

