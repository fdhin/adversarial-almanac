---
tool: "sploitego"
build-system: "scripts"
upstream: "https://github.com/allfro/sploitego"
recipe: "blackarch"
source: "https://github.com/allfro/sploitego"
license: "GPL2"
blackarch-pkgver: "153.d9568dc"
---

# Installing sploitego

> Maltego Penetration Testing Transforms.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sploitego) (version `153.d9568dc`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S sploitego    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/allfro/sploitego) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `amap`
  - `metasploit`
  - `nmap`
  - `p0f`
  - `python2`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[sploitego]] · [[BlackArch Tools]]

