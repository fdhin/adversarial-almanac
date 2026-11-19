---
tool: "credmaster"
build-system: "scripts"
upstream: "https://github.com/knavesec/CredMaster"
recipe: "blackarch"
source: "https://github.com/knavesec/CredMaster"
license: "custom:unknown"
blackarch-pkgver: "206.0a79f34"
---

# Installing credmaster

> Refactored & improved CredKing password spraying tool, uses FireProx APIs to rotate IP addresses, stay anonymous, and beat throttling.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/credmaster) (version `206.0a79f34`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S credmaster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/knavesec/CredMaster
cd CredMaster
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[credmaster]] · [[BlackArch Tools]]

