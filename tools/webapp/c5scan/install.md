---
tool: "c5scan"
build-system: "scripts"
upstream: "https://github.com/auraltension/c5scan"
recipe: "blackarch"
source: "https://github.com/auraltension/c5scan"
license: "GPL2"
blackarch-pkgver: "30.be8845c"
---

# Installing c5scan

> Vulnerability scanner and information gatherer for the Concrete5 CMS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/c5scan) (version `30.be8845c`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S c5scan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/auraltension/c5scan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-httplib2, python2-lxml, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[c5scan]] · [[BlackArch Tools]]

