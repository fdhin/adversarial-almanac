---
tool: "gtalk-decode"
build-system: "windows"
upstream: "http://packetstormsecurity.com/files/119154/Google-Talk-Decoder.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/Win/gtalk_decode.zip"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing gtalk-decode

> Google Talk decoder tool that demonstrates recovering passwords from accounts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gtalk-decode) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gtalk-decode    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://packetstormsecurity.com/files/119154/Google-Talk-Decoder.html
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[gtalk-decode]] · [[BlackArch Tools]]

