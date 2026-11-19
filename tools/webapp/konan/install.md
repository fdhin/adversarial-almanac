---
tool: "konan"
build-system: "scripts"
upstream: "https://github.com/m4ll0k/Konan"
recipe: "blackarch"
source: "https://github.com/m4ll0k/Konan"
license: "custom:unknown"
blackarch-pkgver: "23.7b5ac80"
---

# Installing konan

> Advanced Web Application Dir Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/konan) (version `23.7b5ac80`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S konan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/m4ll0k/Konan.git
cd Konan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-colorama, python2-pathlib, python2-requests, python2-urllib3 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[konan]] · [[BlackArch Tools]]

