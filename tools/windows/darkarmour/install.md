---
tool: "darkarmour"
build-system: "windows"
upstream: "https://github.com/bats3c/darkarmour"
recipe: "blackarch"
source: "https://github.com/bats3c/darkarmour"
license: "MIT"
blackarch-pkgver: "4.f10228a"
---

# Installing darkarmour

> Store and execute an encrypted windows binary from inside memory, without a single bit touching disk.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/darkarmour) (version `4.f10228a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S darkarmour    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/bats3c/darkarmour
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mingw-w64-binutils`
  - `mingw-w64-crt`
  - `mingw-w64-gcc`
  - `mingw-w64-headers`
  - `mingw-w64-winpthreads`
  - `osslsigncode`
  - `python`
  - `upx`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[darkarmour]] · [[BlackArch Tools]]

