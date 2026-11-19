---
tool: "dpscan"
build-system: "scripts"
upstream: "https://github.com/insaneisnotfree/Blue-Sky-Information-Security"
recipe: "blackarch"
source: "https://github.com/insaneisnotfree/Blue-Sky-Information-Security"
license: "GPL2"
blackarch-pkgver: "0.1"
---

# Installing dpscan

> Drupal Vulnerability Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dpscan) (version `0.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S dpscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/insaneisnotfree/Blue-Sky-Information-Security.git
cd Blue-Sky-Information-Security
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[dpscan]] · [[BlackArch Tools]]

