---
tool: "iis-shortname-scanner"
build-system: "scripts"
upstream: "https://github.com/lijiejie/IIS_shortname_Scanner"
recipe: "blackarch"
source: "https://github.com/lijiejie/IIS_shortname_Scanner"
license: "custom:unknown"
blackarch-pkgver: "5.4ad4937"
---

# Installing iis-shortname-scanner

> An IIS shortname Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/iis-shortname-scanner) (version `5.4ad4937`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S iis-shortname-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lijiejie/IIS_shortname_Scanner.git
cd IIS_shortname_Scanner
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[iis-shortname-scanner]] · [[BlackArch Tools]]

