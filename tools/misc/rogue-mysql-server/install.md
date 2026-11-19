---
tool: "rogue-mysql-server"
build-system: "scripts"
upstream: "https://github.com/Gifts/Rogue-MySql-Server"
recipe: "blackarch"
source: "https://github.com/Gifts/Rogue-MySql-Server"
license: "BSD"
blackarch-pkgver: "2.78ebbfc"
---

# Installing rogue-mysql-server

> A rogue MySQL server written in Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rogue-mysql-server) (version `2.78ebbfc`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S rogue-mysql-server    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Gifts/Rogue-MySql-Server.git
cd Rogue-MySql-Server
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[rogue-mysql-server]] · [[BlackArch Tools]]

