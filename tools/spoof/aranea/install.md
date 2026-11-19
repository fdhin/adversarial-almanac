---
tool: "aranea"
build-system: "scripts"
upstream: "https://github.com/TigerSecurity"
recipe: "blackarch"
source: "https://github.com/TigerSecurity"
license: "custom:unknown"
blackarch-pkgver: "6.469b9ee"
---

# Installing aranea

> A fast and clean dns spoofing tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aranea) (version `6.469b9ee`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S aranea    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/TigerSecurity) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[aranea]] · [[BlackArch Tools]]

