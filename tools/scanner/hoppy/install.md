---
tool: "hoppy"
build-system: "scripts"
upstream: "https://labs.portcullis.co.uk/downloads/"
recipe: "blackarch"
source: "https://labs.portcullis.co.uk/downloads/"
license: "GPL2"
blackarch-pkgver: "1.8.1"
---

# Installing hoppy

> A python script which tests http methods for configuration issues leaking information or just to see if they are enabled.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hoppy) (version `1.8.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S hoppy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://labs.portcullis.co.uk/downloads/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pyopenssl (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[hoppy]] · [[BlackArch Tools]]

