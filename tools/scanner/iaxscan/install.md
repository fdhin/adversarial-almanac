---
tool: "iaxscan"
build-system: "scripts"
upstream: "http://code.google.com/p/iaxscan/"
recipe: "blackarch"
source: "http://code.google.com/p/iaxscan/"
license: "mozilla"
blackarch-pkgver: "0.02"
provides: ["iax-scan-hosts", "iax-scan-users"]
---

# Installing iaxscan

> A Python based scanner for detecting live IAX/2 hosts and then enumerating (by bruteforce) users on those hosts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/iaxscan) (version `0.02`, license mozilla).

## Arch / BlackArch

```bash
sudo pacman -S iaxscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://code.google.com/p/iaxscan/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/iax-scan-hosts`, `/usr/bin/iax-scan-users`
- Data: `/usr/share/iaxscan`

Backlinks: [[scanner]] · [[iaxscan]] · [[BlackArch Tools]]

