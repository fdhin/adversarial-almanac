---
tool: "harness"
build-system: "scripts"
upstream: "https://github.com/Rich5/Harness"
recipe: "blackarch"
aur-package: "harness"
source: "https://github.com/Rich5/Harness"
license: "MIT"
blackarch-pkgver: "19.ed2a6aa"
---

# Installing harness

> Interactive remote PowerShell Payload.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/harness) (version `19.ed2a6aa`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S harness    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Rich5/Harness.git
cd Harness
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[harness]] · [[BlackArch Tools]]

