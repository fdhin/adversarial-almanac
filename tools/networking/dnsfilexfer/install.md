---
tool: "dnsfilexfer"
build-system: "scripts"
upstream: "https://github.com/leonjza/dnsfilexfer"
recipe: "blackarch"
source: "https://github.com/leonjza/dnsfilexfer"
license: "MIT"
blackarch-pkgver: "24.126edcd"
provides: ["dnsfilexfer-recv", "dnsfilexfer-send"]
---

# Installing dnsfilexfer

> File transfer via DNS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsfilexfer) (version `24.126edcd`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dnsfilexfer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/leonjza/dnsfilexfer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dnspython, python2-pycryptodome (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/dnsfilexfer-recv`, `/usr/bin/dnsfilexfer-send`

Backlinks: [[networking]] · [[dnsfilexfer]] · [[BlackArch Tools]]

