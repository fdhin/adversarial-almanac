---
tool: "ghostpack"
build-system: "windows"
upstream: "https://github.com/r3motecontrol/Ghostpack-CompiledBinaries"
recipe: "blackarch"
source: "https://github.com/r3motecontrol/Ghostpack-CompiledBinaries"
license: "BSD3-Clause"
blackarch-pkgver: "123.20a5f0a"
---

# Installing ghostpack

> Compiled Binaries for Ghostpack (.NET v4.8.1).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ghostpack) (version `123.20a5f0a`, license BSD3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S ghostpack    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/r3motecontrol/Ghostpack-CompiledBinaries
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[ghostpack]] · [[BlackArch Tools]]

