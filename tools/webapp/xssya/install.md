---
tool: "xssya"
build-system: "scripts"
upstream: "https://github.com/yehia-mamdouh/XSSYA"
recipe: "blackarch"
source: "https://github.com/yehia-mamdouh/XSSYA"
license: "custom"
blackarch-pkgver: "13.cd62817"
---

# Installing xssya

> A Cross Site Scripting Scanner & Vulnerability Confirmation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xssya) (version `13.cd62817`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S xssya    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/yehia-mamdouh/XSSYA-V-2.0.git
cd XSSYA-V-2.0
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-colorama, python2-gdshortener (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/xssya`

Backlinks: [[webapp]] · [[xssya]] · [[BlackArch Tools]]

