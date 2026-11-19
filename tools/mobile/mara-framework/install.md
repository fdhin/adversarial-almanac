---
tool: "mara-framework"
build-system: "scripts"
upstream: "https://github.com/xtiankisutsa/MARA_Framework"
recipe: "blackarch"
source: "https://github.com/xtiankisutsa/MARA_Framework"
license: "GPL3"
blackarch-pkgver: "176.ac4ac88"
---

# Installing mara-framework

> A Mobile Application Reverse engineering and Analysis Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mara-framework) (version `176.ac4ac88`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S mara-framework    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/xtiankisutsa/MARA_Framework.git
cd MARA_Framework
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `figlet`
  - `gtk2`
  - `python2`
  - `python3`
  - `tree`
- ⚠️ Python 2 only deps: python2-pydot (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[mara-framework]] · [[BlackArch Tools]]

