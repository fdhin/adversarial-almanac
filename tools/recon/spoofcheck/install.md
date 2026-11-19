---
tool: "spoofcheck"
build-system: "scripts"
upstream: "https://github.com/bishopfox/spoofcheck"
recipe: "blackarch"
source: "https://github.com/bishopfox/spoofcheck"
license: "MIT"
blackarch-pkgver: "16.8cce591"
---

# Installing spoofcheck

> Simple script that checks a domain for email protections.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spoofcheck) (version `16.8cce591`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S spoofcheck    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/bishopfox/spoofcheck) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-colorama, python2-dnslib, python2-emailprotectionslib, python2-idna, python2-requests, python2-requests-file, python2-setuptools, python2-tldextract (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[spoofcheck]] · [[BlackArch Tools]]

