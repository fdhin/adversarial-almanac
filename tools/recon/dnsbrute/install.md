---
tool: "dnsbrute"
build-system: "scripts"
upstream: "https://github.com/d4rkcat/dnsbrute"
recipe: "blackarch"
source: "https://github.com/d4rkcat/dnsbrute"
license: "GPL2"
blackarch-pkgver: "2.b1dc84a"
---

# Installing dnsbrute

> Multi-theaded DNS bruteforcing, average speed 80 lookups/second with 40 threads.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsbrute) (version `2.b1dc84a`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S dnsbrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/d4rkcat/dnsbrute) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dnspython (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[dnsbrute]] · [[BlackArch Tools]]

