---
tool: "remot3d"
build-system: "scripts"
upstream: "https://github.com/KeepWannabe/Remot3d"
recipe: "blackarch"
source: "https://github.com/KeepWannabe/Remot3d"
license: "GPL3"
blackarch-pkgver: "38.a707ef7"
---

# Installing remot3d

> An Simple Exploit for PHP Language.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/remot3d) (version `38.a707ef7`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S remot3d    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/KeepWannabe/Remot3d.git
cd Remot3d
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[remot3d]] · [[BlackArch Tools]]

