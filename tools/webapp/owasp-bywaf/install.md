---
tool: "owasp-bywaf"
build-system: "scripts"
upstream: "https://github.com/depasonico/OWASP-ByWaf"
recipe: "blackarch"
source: "https://github.com/depasonico/OWASP-ByWaf"
license: "custom"
blackarch-pkgver: "26.e730d1b"
---

# Installing owasp-bywaf

> A web application penetration testing framework (WAPTF).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/owasp-bywaf) (version `26.e730d1b`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S owasp-bywaf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/depasonico/OWASP-ByWaf.git
cd OWASP-ByWaf
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-setuptools, python2-sqlalchemy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[owasp-bywaf]] · [[BlackArch Tools]]

