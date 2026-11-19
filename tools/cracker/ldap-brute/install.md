---
tool: "ldap-brute"
build-system: "scripts"
upstream: "https://github.com/droope/ldap-brute"
recipe: "blackarch"
source: "https://github.com/droope/ldap-brute"
license: "GPL3"
blackarch-pkgver: "21.acc06e3"
---

# Installing ldap-brute

> A semi fast tool to bruteforce values of LDAP injections over HTTP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ldap-brute) (version `21.acc06e3`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ldap-brute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/droope/ldap-brute) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-httmock, python2-mock, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[ldap-brute]] · [[BlackArch Tools]]

