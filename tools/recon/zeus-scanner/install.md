---
tool: "zeus-scanner"
build-system: "scripts"
upstream: "https://github.com/Ekultek/Zeus-Scanner"
recipe: "blackarch"
aur-package: "zeus-scanner"
source: "https://github.com/Ekultek/Zeus-Scanner"
license: "GPL3"
blackarch-pkgver: "414.21b8756"
---

# Installing zeus-scanner

> Advanced dork searching utility.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zeus-scanner) (version `414.21b8756`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S zeus-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Ekultek/Zeus-Scanner.git
cd Zeus-Scanner
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `lxml`, `psutil`, `python-nmap`, `pyvirtualdisplay`, `requests`, `selenium`, `whichcraft`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `nmap`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[zeus-scanner]] · [[BlackArch Tools]]

