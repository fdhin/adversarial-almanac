---
tool: "arcane"
build-system: "scripts"
upstream: "https://github.com/tokyoneon/Arcane"
recipe: "blackarch"
source: "https://github.com/tokyoneon/Arcane"
license: "custom:unknown"
blackarch-pkgver: "2.750cb9f"
---

# Installing arcane

> Backdoor iOS packages and create the necessary resources for APT repositories.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/arcane) (version `2.750cb9f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S arcane    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/tokyoneon/Arcane.git
cd Arcane
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bzip2`
  - `openbsd-netcat`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[arcane]] · [[BlackArch Tools]]

