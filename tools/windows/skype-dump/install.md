---
tool: "skype-dump"
build-system: "windows"
upstream: "http://packetstormsecurity.com/files/119155/Skype-Hash-Dumper-1.0.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/Win/skype_dump.zip"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing skype-dump

> This is a tool that demonstrates dumping MD5 password hashes from the configuration file in Skype.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/skype-dump) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S skype-dump    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://packetstormsecurity.com/files/119155/Skype-Hash-Dumper.0.html
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[skype-dump]] · [[BlackArch Tools]]

