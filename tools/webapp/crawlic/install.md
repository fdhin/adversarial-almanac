---
tool: "crawlic"
build-system: "scripts"
upstream: "https://github.com/Ganapati/Crawlic"
recipe: "blackarch"
source: "https://github.com/Ganapati/Crawlic"
license: "custom:unknown"
blackarch-pkgver: "51.739fe2b"
---

# Installing crawlic

> Web recon tool (find temporary files, parse robots.txt, search folders, google dorks and search domains hosted on same server).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crawlic) (version `51.739fe2b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S crawlic    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Ganapati/Crawlic.git
cd Crawlic
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[crawlic]] · [[BlackArch Tools]]

