---
tool: "androick"
build-system: "scripts"
upstream: "https://github.com/Flo354/Androick"
recipe: "blackarch"
source: "https://github.com/Flo354/Androick"
license: "GPL-3.0-or-later"
blackarch-pkgver: "8.522cfb4"
---

# Installing androick

> A python tool to help in forensics analysis on android.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/androick) (version `8.522cfb4`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S androick    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Flo354/Androick.git
cd Androick
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `android-sdk`
  - `android-sdk-build-tools`
  - `android-sdk-platform-tools`
  - `python2`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel
- ⚠️ Python 2 only deps: python2-magic (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[androick]] · [[BlackArch Tools]]

