---
tool: "gooscan"
build-system: "other"
upstream: "http://johnny.ihackstuff.com/downloads/task,doc_details&Itemid=/gid,28/"
recipe: "blackarch"
source: "http://johnny.ihackstuff.com/downloads/task,doc_details&Itemid=/gid,28/"
license: "custom:unknown"
blackarch-pkgver: "1.0.9"
---

# Installing gooscan

> A tool that automates queries against Google search appliances, but with a twist.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gooscan) (version `1.0.9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gooscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://johnny.ihackstuff.com/downloads/task,doc_details&Itemid=/gid,28/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc $CFLAGS "$pkgname.c" -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[gooscan]] · [[BlackArch Tools]]

