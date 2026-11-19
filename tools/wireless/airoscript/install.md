---
tool: "airoscript"
build-system: "perl"
upstream: "http://midnightresearch.com/projects/wicrawl/"
recipe: "blackarch"
source: "http://midnightresearch.com/projects/wicrawl/"
license: "GPL-1.0-or-later"
blackarch-pkgver: "45.0a122ee"
---

# Installing airoscript

> A script to simplify the use of aircrack-ng tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/airoscript) (version `45.0a122ee`, license GPL-1.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S airoscript    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone http://github.com/patrickhno/Airoscript.git
cd Airoscript
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `dhclient`
  - `mdk3`
  - `perl-yaml-syck`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[airoscript]] · [[BlackArch Tools]]

