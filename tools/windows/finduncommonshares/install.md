---
tool: "finduncommonshares"
build-system: "windows"
upstream: "https://github.com/p0dalirius/pyFindUncommonShares"
recipe: "blackarch"
aur-package: "finduncommonshares"
source: "https://github.com/p0dalirius/pyFindUncommonShares"
license: "GPL-2.0-only"
blackarch-pkgver: "3.3.r1.gaef0f0d"
---

# Installing finduncommonshares

> Python script allowing to quickly find uncommon shares in vast Windows Domains.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/finduncommonshares) (version `3.3.r1.gaef0f0d`, license GPL-2.0-only).

## Arch / BlackArch

```bash
sudo pacman -S finduncommonshares    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/p0dalirius/pyFindUncommonShares
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `pycryptodome`, `sectools`, `xlsxwriter`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[finduncommonshares]] · [[BlackArch Tools]]

