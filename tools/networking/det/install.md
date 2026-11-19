---
tool: "det"
build-system: "scripts"
upstream: "https://github.com/sensepost/det"
recipe: "blackarch"
source: "https://github.com/sensepost/det"
license: "custom:unknown"
blackarch-pkgver: "31.417cbce"
---

# Installing det

> (extensible) Data Exfiltration Toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/det) (version `31.417cbce`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S det    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sensepost/det) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dnslib, python2-pycryptodome, python2-pysocks, python2-scapy, python2-tweepy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[det]] · [[BlackArch Tools]]

