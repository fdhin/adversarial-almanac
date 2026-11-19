---
tool: "ollydbg"
build-system: "scripts"
upstream: "http://www.ollydbg.de/"
recipe: "blackarch"
source: "http://www.ollydbg.de/"
license: "custom"
blackarch-pkgver: "201g"
kali-package: "ollydbg"
kali-version: "1.10-1kali5"
---

# Installing ollydbg

> A 32-bit assembler-level analysing debugger.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ollydbg) (version `201g`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S ollydbg    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install ollydbg    # Kali package version 1.10-1kali5
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.ollydbg.de) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unzip`
  - `wine`
  - `wine-mono`
  - `wine_gecko`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[debugger]] · [[ollydbg]] · [[BlackArch Tools]]

