---
tool: "infip"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/104927/infIP-0.1-Blacklist-Checker.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/utilities/infIP.rar"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing infip

> A python script that checks output from netstat against RBLs from Spamhaus.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/infip) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S infip    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/104927/infIP.1-Blacklist-Checker.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup3, python2-httplib2 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[infip]] · [[BlackArch Tools]]

