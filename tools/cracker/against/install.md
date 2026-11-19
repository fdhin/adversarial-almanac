---
tool: "against"
build-system: "scripts"
upstream: "http://nullsecurity.net/tools/cracker.html"
recipe: "blackarch"
source: "http://nullsecurity.net/tools/cracker.html"
license: "custom"
blackarch-pkgver: "0.2"
---

# Installing against

> A very fast ssh attacking script which includes a multithreaded port scanning module (tcp connect) for discovering possible targets and a multithreaded brute-forcing module which attacks parallel all discovered hosts or given ip addresses from a list.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/against) (version `0.2`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S against    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://nullsecurity.net/tools/cracker.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- ⚠️ Python 2 only deps: python2-paramiko (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[against]] · [[BlackArch Tools]]

