---
tool: "gwtenum"
build-system: "scripts"
upstream: "http://www.gdssecurity.com/l/t/d.php?k=GwtEnum"
recipe: "blackarch"
source: "http://www.gdssecurity.com/l/t/d.php?k=GwtEnum"
license: "CCPL"
blackarch-pkgver: "7.f27a5aa"
---

# Installing gwtenum

> Enumeration of GWT-RCP method calls.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gwtenum) (version `7.f27a5aa`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S gwtenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/GDSSecurity/GWT-Penetration-Testing-Toolset.git
cd GWT-Penetration-Testing-Toolset
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[gwtenum]] · [[BlackArch Tools]]

