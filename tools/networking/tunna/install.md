---
tool: "tunna"
build-system: "scripts"
upstream: "https://github.com/SECFORCE/Tunna"
recipe: "blackarch"
source: "https://github.com/SECFORCE/Tunna"
license: "GPL3"
blackarch-pkgver: "41.cba006d"
---

# Installing tunna

> a set of tools which will wrap and tunnel any TCP communication over HTTP. It can be used to bypass network restrictions in fully firewalled environments.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tunna) (version `41.cba006d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S tunna    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/SECFORCE/Tunna.git
cd Tunna
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[tunna]] · [[BlackArch Tools]]

