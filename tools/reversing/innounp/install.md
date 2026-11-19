---
tool: "innounp"
build-system: "scripts"
upstream: "https://sourceforge.net/projects/innounp/files/innounp/"
recipe: "blackarch"
source: "https://sourceforge.net/projects/innounp/files/innounp/"
license: "GPL2"
blackarch-pkgver: "0.50"
---

# Installing innounp

> Inno Setup Unpacker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/innounp) (version `0.50`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S innounp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://sourceforge.net/projects/innounp/files/innounp/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unrar`
  - `wine`
  - `wine-mono`
  - `wine_gecko`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[reversing]] · [[innounp]] · [[BlackArch Tools]]

