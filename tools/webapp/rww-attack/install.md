---
tool: "rww-attack"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/79021/Remote-Web-Workplace-Attack-Tool.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/79021/Remote-Web-Workplace-Attack-Tool.html"
license: "GPL"
blackarch-pkgver: "0.9.2"
---

# Installing rww-attack

> Performs a dictionary attack against a live Microsoft Windows Small Business Server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rww-attack) (version `0.9.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S rww-attack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/79021/Remote-Web-Workplace-Attack-Tool.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `unzip`
- ⚠️ Python 2 only deps: python2-pycurl (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[rww-attack]] · [[BlackArch Tools]]

