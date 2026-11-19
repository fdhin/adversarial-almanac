---
tool: "jynx2"
build-system: "scripts"
upstream: "https://github.com/chokepoint/Jynx2"
recipe: "blackarch"
source: "https://github.com/chokepoint/Jynx2"
license: "custom:unknown"
blackarch-pkgver: "1.2bcbebf"
---

# Installing jynx2

> An expansion of the original Jynx LD_PRELOAD rootkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jynx2) (version `1.2bcbebf`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S jynx2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/chokepoint/Jynx2.git
cd Jynx2
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[jynx2]] · [[BlackArch Tools]]

