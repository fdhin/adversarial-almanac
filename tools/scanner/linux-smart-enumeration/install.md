---
tool: "linux-smart-enumeration"
build-system: "scripts"
upstream: "https://github.com/diego-treitos/linux-smart-enumeration"
recipe: "blackarch"
source: "https://github.com/diego-treitos/linux-smart-enumeration"
license: "GPL2"
blackarch-pkgver: "300.b83a26f"
---

# Installing linux-smart-enumeration

> Linux enumeration tool for pentesting and CTFs with verbosity levels.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/linux-smart-enumeration) (version `300.b83a26f`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S linux-smart-enumeration    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/diego-treitos/linux-smart-enumeration) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[linux-smart-enumeration]] · [[BlackArch Tools]]

