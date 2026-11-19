---
tool: "ldapconsole"
build-system: "scripts"
upstream: "https://github.com/p0dalirius/ldapconsole"
recipe: "blackarch"
source: "https://github.com/p0dalirius/ldapconsole"
license: "GPL-2.0-only"
blackarch-pkgver: "2.1.0.r12.g1b0886a"
---

# Installing ldapconsole

> Script allows you to perform custom LDAP requests to a Windows domain.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ldapconsole) (version `2.1.0.r12.g1b0886a`, license GPL-2.0-only).

## Arch / BlackArch

```bash
sudo pacman -S ldapconsole    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/p0dalirius/ldapconsole) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap3`, `pycryptodome`, `sectools`, `xlsxwriter`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[ldapconsole]] · [[BlackArch Tools]]

