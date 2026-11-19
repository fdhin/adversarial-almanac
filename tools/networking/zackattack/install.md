---
tool: "zackattack"
build-system: "scripts"
upstream: "https://github.com/urbanesec/ZackAttack/"
recipe: "blackarch"
source: "https://github.com/urbanesec/ZackAttack/"
license: "custom:unknown"
blackarch-pkgver: "5.1f96c14"
---

# Installing zackattack

> A new tool set to do NTLM Authentication relaying unlike any other tool currently out there.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zackattack) (version `5.1f96c14`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S zackattack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/urbanesec/ZackAttack.git
cd ZackAttack
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-sqlite3`
  - `ruby-webrick`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[zackattack]] · [[BlackArch Tools]]

