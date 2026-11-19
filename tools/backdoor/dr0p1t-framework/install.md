---
tool: "dr0p1t-framework"
build-system: "scripts"
upstream: "https://github.com/D4Vinci/Dr0p1t-Framework"
recipe: "blackarch"
source: "https://github.com/D4Vinci/Dr0p1t-Framework"
license: "custom:unknown"
blackarch-pkgver: "44.db9bc2d"
provides: ["dr0p1t"]
---

# Installing dr0p1t-framework

> A framework that creates a dropper that bypass most AVs, some sandboxes and have some tricks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dr0p1t-framework) (version `44.db9bc2d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dr0p1t-framework    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/D4Vinci/Dr0p1t-Framework.git
cd Dr0p1t-Framework
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `pyinstaller`
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/dr0p1t`

Backlinks: [[backdoor]] · [[dr0p1t-framework]] · [[BlackArch Tools]]

