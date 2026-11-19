---
tool: "nxcrypt"
build-system: "scripts"
upstream: "https://github.com/Hadi999/NXcrypt"
recipe: "blackarch"
source: "https://github.com/Hadi999/NXcrypt"
license: "custom:unknown"
blackarch-pkgver: "32.6ae06b5"
---

# Installing nxcrypt

> Python backdoor framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nxcrypt) (version `32.6ae06b5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S nxcrypt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Hadi999/NXcrypt.git
cd NXcrypt
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[nxcrypt]] · [[BlackArch Tools]]

