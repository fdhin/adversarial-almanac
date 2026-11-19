---
tool: "vscan"
build-system: "scripts"
upstream: "https://github.com/pasjtene/Vscan"
recipe: "blackarch"
source: "https://github.com/pasjtene/Vscan"
license: "custom:unknown"
blackarch-pkgver: "10.da4e47e"
---

# Installing vscan

> HTTPS / Vulnerability scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vscan) (version `10.da4e47e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S vscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/pasjtene/Vscan.git
cd Vscan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `apache-ant`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[vscan]] · [[BlackArch Tools]]

