---
tool: "darkbing"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/111510/darkBing-SQL-Scanner-0.1.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/111510/darkBing-SQL-Scanner-0.1.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing darkbing

> A tool written in python that leverages bing for mining data on systems that may be susceptible to SQL injection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/darkbing) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S darkbing    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/111510/darkBing-SQL-Scanner.1.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-lxml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[darkbing]] · [[BlackArch Tools]]

