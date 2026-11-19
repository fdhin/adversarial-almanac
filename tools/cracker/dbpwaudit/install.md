---
tool: "dbpwaudit"
build-system: "scripts"
upstream: "http://www.cqure.net/wp/dbpwaudit/"
recipe: "blackarch"
source: "http://www.cqure.net/wp/dbpwaudit/"
license: "GPL"
blackarch-pkgver: "0.8"
---

# Installing dbpwaudit

> A Java tool that allows you to perform online audits of password quality for several database engines.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dbpwaudit) (version `0.8`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dbpwaudit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.cqure.net/wp/dbpwaudit/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[dbpwaudit]] · [[BlackArch Tools]]

