---
tool: "epicwebhoneypot"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/epicwebhoneypot/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/epicwebhoneypot/"
license: "GPL2"
blackarch-pkgver: "2.0a"
---

# Installing epicwebhoneypot

> Tool which aims to lure attackers using various types of web vulnerability scanners by tricking them into believing that they have found a vulnerability on a host.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/epicwebhoneypot) (version `2.0a`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S epicwebhoneypot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/epicwebhoneypot/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[epicwebhoneypot]] · [[BlackArch Tools]]

