---
tool: "de4dot"
build-system: "windows"
upstream: "https://github.com/de4dot/de4dot"
recipe: "blackarch"
source: "https://github.com/de4dot/de4dot"
license: "GPL3"
blackarch-pkgver: "2090.b7d5728"
---

# Installing de4dot

> .NET deobfuscator and unpacker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/de4dot) (version `2090.b7d5728`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S de4dot    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/de4dot/de4dot
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`, `mono`
- **System libraries** (Arch name — translate for your distro):
  - `mono-msbuild`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[de4dot]] · [[BlackArch Tools]]

