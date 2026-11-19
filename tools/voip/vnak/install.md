---
tool: "vnak"
build-system: "scripts"
upstream: "https://www.isecpartners.com/vnak.html"
recipe: "blackarch"
source: "https://www.isecpartners.com/vnak.html"
license: "GPL"
blackarch-pkgver: "1.cf0fda7"
---

# Installing vnak

> Aim is to be the one tool a user needs to attack multiple VoIP protocols.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vnak) (version `1.cf0fda7`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S vnak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.isecpartners.com/vnak.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
- ⚠️ Python 2 only deps: python2-dpkt, python2-pypcap (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[vnak]] · [[BlackArch Tools]]

