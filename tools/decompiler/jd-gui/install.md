---
tool: "jd-gui"
build-system: "scripts"
upstream: "https://github.com/java-decompiler/jd-gui"
recipe: "blackarch"
aur-package: "jd-gui"
source: "https://github.com/java-decompiler/jd-gui"
license: "custom"
blackarch-pkgver: "1.6.6"
kali-package: "jd-gui"
kali-version: "1.6.6-0kali1"
---

# Installing jd-gui

> A standalone graphical utility that displays Java source codes of .class files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jd-gui) (version `1.6.6`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S jd-gui    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install jd-gui    # Kali package version 1.6.6-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/java-decompiler/jd-gui) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `libxtst`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[jd-gui]] · [[BlackArch Tools]]

