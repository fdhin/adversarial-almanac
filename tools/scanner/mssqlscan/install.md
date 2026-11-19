---
tool: "mssqlscan"
build-system: "scripts"
upstream: "http://www.cqure.net/wp/mssqlscan/"
recipe: "blackarch"
source: "http://www.cqure.net/wp/mssqlscan/"
license: "GPL"
blackarch-pkgver: "0.8.4"
---

# Installing mssqlscan

> A small multi-threaded tool that scans for Microsoft SQL Servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mssqlscan) (version `0.8.4`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S mssqlscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.cqure.net/wp/mssqlscan/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[mssqlscan]] · [[BlackArch Tools]]

