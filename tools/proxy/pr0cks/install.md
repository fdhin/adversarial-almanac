---
tool: "pr0cks"
build-system: "scripts"
upstream: "https://github.com/n1nj4sec/pr0cks"
recipe: "blackarch"
source: "https://github.com/n1nj4sec/pr0cks"
license: "GPL2"
blackarch-pkgver: "3.3b740fa"
---

# Installing pr0cks

> python script setting up a transparent proxy to forward all TCP and DNS traffic through a SOCKS / SOCKS5 or HTTP(CONNECT) proxy using iptables -j REDIRECT target.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pr0cks) (version `3.3b740fa`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S pr0cks    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/n1nj4sec/pr0cks) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `iptables`
  - `python2`
- ⚠️ Python 2 only deps: python2-dnslib (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[pr0cks]] · [[BlackArch Tools]]

