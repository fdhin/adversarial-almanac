---
tool: "shad0w"
build-system: "windows"
upstream: "https://github.com/bats3c/shad0w"
recipe: "blackarch"
source: "https://packages.microsoft.com/config/ubuntu/20.10/packages-microsoft-prod.deb"
license: "MIT"
blackarch-pkgver: "387.d35b9dc"
---

# Installing shad0w

> A modular C2 framework designed to successfully operate on mature environments.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shad0w) (version `387.d35b9dc`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S shad0w    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/bats3c/shad0w
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `cffi`, `flask`, `pefile`, `prettytable`, `prompt_toolkit`, `pygments`, `pyopenssl`, `requests`, `setuptools`
- **Build tools**: `dotnet-sdk`, `git`, `mono`
- **System libraries** (Arch name — translate for your distro):
  - `donut`
  - `metasploit`
  - `mingw-w64-gcc`
  - `nasm`
  - `python`
  - `upx`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[shad0w]] · [[BlackArch Tools]]

