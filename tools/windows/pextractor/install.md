---
tool: "pextractor"
build-system: "windows"
upstream: "http://packetstormsecurity.com/files/62977/PExtractor_v0.18b_binary_and_src.rar.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/62977/PExtractor_v0.18b_binary_and_src.rar.html"
license: "custom:unknown"
blackarch-pkgver: "0.18b"
---

# Installing pextractor

> A forensics tool that can extract all files from an executable file created by a joiner or similar.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pextractor) (version `0.18b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pextractor    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://packetstormsecurity.com/files/62977/PExtractor_v0.18b_binary_and_src.rar.html
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unrar`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[pextractor]] · [[BlackArch Tools]]

