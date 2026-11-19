---
tool: "pdgmail"
build-system: "scripts"
upstream: "http://www.jeffbryner.com/code/pdgmail"
recipe: "blackarch"
source: "http://www.jeffbryner.com/code/pdgmail"
license: "GPL2"
blackarch-pkgver: "1.0"
---

# Installing pdgmail

> A password dictionary attack tool that targets windows authentication via the SMB protocol.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pdgmail) (version `1.0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S pdgmail    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.jeffbryner.com/code/pdgmail) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[pdgmail]] · [[BlackArch Tools]]

