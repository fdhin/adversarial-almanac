---
tool: "mkbrutus"
build-system: "scripts"
upstream: "http://mkbrutusproject.github.io/MKBRUTUS/"
recipe: "blackarch"
source: "http://mkbrutusproject.github.io/MKBRUTUS/"
license: "GPL3"
blackarch-pkgver: "27.ddd5f8e"
---

# Installing mkbrutus

> Password bruteforcer for MikroTik devices or boxes running RouterOS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mkbrutus) (version `27.ddd5f8e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S mkbrutus    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mkbrutusproject/MKBRUTUS.git
cd MKBRUTUS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python3`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[mkbrutus]] · [[BlackArch Tools]]

