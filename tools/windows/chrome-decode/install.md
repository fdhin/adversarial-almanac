---
tool: "chrome-decode"
build-system: "windows"
upstream: "http://packetstormsecurity.com/files/119153/Chrome-Web-Browser-Decoder.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/119153/Chrome-Web-Browser-Decoder.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing chrome-decode

> Chrome web browser decoder tool that demonstrates recovering passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chrome-decode) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S chrome-decode    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://packetstormsecurity.com/files/119153/Chrome-Web-Browser-Decoder.html
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[chrome-decode]] · [[BlackArch Tools]]

