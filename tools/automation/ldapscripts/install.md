---
tool: "ldapscripts"
build-system: "scripts"
upstream: "https://sourceforge.net/projects/ldapscripts"
recipe: "blackarch"
source: "https://sourceforge.net/projects/ldapscripts"
license: "GPL2"
blackarch-pkgver: "2.0.8"
---

# Installing ldapscripts

> Simple shell scripts to handle POSIX entries in an LDAP directory.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ldapscripts) (version `2.0.8`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S ldapscripts    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://sourceforge.net/projects/ldapscripts) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openldap`
  - `sharutils`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[automation]] · [[ldapscripts]] · [[BlackArch Tools]]

