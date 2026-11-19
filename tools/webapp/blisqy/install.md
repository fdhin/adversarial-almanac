---
tool: "blisqy"
build-system: "scripts"
upstream: "https://github.com/JohnTroony/Blisqy"
recipe: "blackarch"
source: "https://github.com/JohnTroony/Blisqy"
license: "custom:unknown"
blackarch-pkgver: "20.e9995fc"
---

# Installing blisqy

> Exploit Time-based blind-SQL injection in HTTP-Headers (MySQL/MariaDB).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/blisqy) (version `20.e9995fc`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S blisqy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/JohnTroony/Blisqy.git
cd Blisqy
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[blisqy]] · [[BlackArch Tools]]

