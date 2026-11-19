---
tool: "sqlpat"
build-system: "other"
upstream: "http://www.cqure.net/wp/sqlpat/"
recipe: "blackarch"
source: "http://www.cqure.net/wp/sqlpat/"
license: "GPL"
blackarch-pkgver: "1.0.1"
provides: ["sqlbf"]
---

# Installing sqlpat

> This tool should be used to audit the strength of Microsoft SQL Server passwords offline.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sqlpat) (version `1.0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S sqlpat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.cqure.net/wp/sqlpat/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd sqlbf

  gcc $CFLAGS bf.c crypto.c sqlbf.c -o sqlbf -I include/ -lpthread -lm -lssl \
    -lcrypto
```

## Installed layout (per recipe)

- Commands: `/usr/bin/sqlbf`

Backlinks: [[cracker]] · [[sqlpat]] · [[BlackArch Tools]]

