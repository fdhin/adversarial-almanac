---
tool: "atstaketools"
build-system: "windows"
upstream: "http://packetstormsecurity.com/files/50718/AtStakeTools.zip.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/50718/AtStakeTools.zip.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing atstaketools

> This is an archive of various @Stake tools that help perform vulnerability scanning and analysis, information gathering, password auditing, and forensics.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/atstaketools) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S atstaketools    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://packetstormsecurity.com/files/50718/AtStakeTools.zip.html
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[atstaketools]] · [[BlackArch Tools]]

