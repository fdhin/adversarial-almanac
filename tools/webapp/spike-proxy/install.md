---
tool: "spike-proxy"
build-system: "scripts"
upstream: "http://www.immunitysec.com/resources-freesoftware.shtml"
recipe: "blackarch"
source: "http://www.immunitysec.com/resources-freesoftware.shtml"
license: "GPL"
blackarch-pkgver: "148"
---

# Installing spike-proxy

> A Proxy for detecting vulnerabilities in web applications

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spike-proxy) (version `148`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S spike-proxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.immunitysec.com/resources-freesoftware.shtml) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `python2`
- ⚠️ Python 2 only deps: python2-pyopenssl (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[spike-proxy]] · [[BlackArch Tools]]

