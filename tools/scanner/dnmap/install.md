---
tool: "dnmap"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/dnmap/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/dnmap/"
license: "GPL3"
blackarch-pkgver: "0.6"
provides: ["dnmap_client", "dnmap_server"]
---

# Installing dnmap

> The distributed nmap framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnmap) (version `0.6`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dnmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/dnmap/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pyopenssl, python2-twistedx (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/dnmap_client`, `/usr/bin/dnmap_server`

Backlinks: [[scanner]] · [[dnmap]] · [[BlackArch Tools]]

