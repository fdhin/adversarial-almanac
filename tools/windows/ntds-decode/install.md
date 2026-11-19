---
tool: "ntds-decode"
build-system: "windows"
upstream: "http://packetstormsecurity.com/files/121543/NTDS-Hash-Decoder-01.b.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/Win/ntds_decode.zip"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing ntds-decode

> This application dumps LM and NTLM hashes from active accounts stored in an Active Directory database.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ntds-decode) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ntds-decode    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://packetstormsecurity.com/files/121543/NTDS-Hash-Decoder.b.html
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[ntds-decode]] · [[BlackArch Tools]]

