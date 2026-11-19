---
tool: "dumpacl"
build-system: "windows"
upstream: "http://www.systemtools.com/cgi-bin/download.pl?DumpAcl"
recipe: "blackarch"
source: "http://www.systemtools.com/download/dumpacl.zip"
license: "custom"
blackarch-pkgver: "0.1"
---

# Installing dumpacl

> Dumps NTs ACLs and audit settings.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dumpacl) (version `0.1`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S dumpacl    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.systemtools.com/cgi-bin/download.pl?DumpAcl
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[dumpacl]] · [[BlackArch Tools]]

