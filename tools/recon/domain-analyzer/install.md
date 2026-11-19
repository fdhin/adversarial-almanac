---
tool: "domain-analyzer"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/domainanalyzer/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/domainanalyzer/"
license: "GPL2"
blackarch-pkgver: "0.8.1"
provides: ["ansistrm.py", "domain-analyzer-crawler", "domain-analyzer-pytext2pdf"]
---

# Installing domain-analyzer

> Finds all the security information for a given domain name.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/domain-analyzer) (version `0.8.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S domain-analyzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/domainanalyzer/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dnspython, python2-httplib2 (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/ansistrm.py`, `/usr/bin/domain-analyzer-crawler`, `/usr/bin/domain-analyzer-pytext2pdf`

Backlinks: [[recon]] · [[domain-analyzer]] · [[BlackArch Tools]]

