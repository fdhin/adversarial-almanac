---
tool: "skiptracer"
build-system: "scripts"
upstream: "https://github.com/84KaliPleXon3/skiptracer"
recipe: "blackarch"
source: "https://github.com/84KaliPleXon3/skiptracer"
license: "GPL"
blackarch-pkgver: "123.ca40957"
---

# Installing skiptracer

> OSINT python2 webscraping framework. Skipping the needs of API keys.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/skiptracer) (version `123.ca40957`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S skiptracer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/84KaliPleXon3/skiptracer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `ipdb`
- **Build tools**: `git`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-cfscrape, python2-click, python2-lxml, python2-numpy, python2-python-docx, python2-requests, python2-simplejson, python2-tqdm, python2-urllib3 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[skiptracer]] · [[BlackArch Tools]]

