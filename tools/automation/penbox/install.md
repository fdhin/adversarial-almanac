---
tool: "penbox"
build-system: "perl"
upstream: "https://github.com/x3omdax/PenBox"
recipe: "blackarch"
source: "https://github.com/x3omdax/PenBox"
license: "MIT"
blackarch-pkgver: "81.3b77c69"
---

# Installing penbox

> A Penetration Testing Framework - The Tool With All The Tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/penbox) (version `81.3b77c69`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S penbox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/x3omdax/PenBox.git
cd PenBox
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `python2`
  - `python3`
  - `ruby`
- ⚠️ Python 2 only deps: python2-lxml, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[penbox]] · [[BlackArch Tools]]

