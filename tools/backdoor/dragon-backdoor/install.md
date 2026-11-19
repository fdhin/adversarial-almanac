---
tool: "dragon-backdoor"
build-system: "scripts"
upstream: "https://github.com/Shellntel/backdoors"
recipe: "blackarch"
source: "https://github.com/Shellntel/backdoors"
license: "custom:unknown"
blackarch-pkgver: "7.c7416b7"
---

# Installing dragon-backdoor

> A sniffing, non binding, reverse down/exec, portknocking service Based on cd00r.c.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dragon-backdoor) (version `7.c7416b7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dragon-backdoor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Shellntel/backdoors.git
cd backdoors
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[backdoor]] · [[dragon-backdoor]] · [[BlackArch Tools]]

