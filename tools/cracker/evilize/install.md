---
tool: "evilize"
build-system: "make"
upstream: "http://www.mathstat.dal.ca/~selinger/md5collision/"
recipe: "blackarch"
source: "http://www.mathstat.dal.ca/~selinger/md5collision/"
license: "GPL2"
blackarch-pkgver: "0.2"
provides: ["evilize-md5coll"]
---

# Installing evilize

> Tool to create MD5 colliding binaries.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/evilize) (version `0.2`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S evilize    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.mathstat.dal.ca/~selinger/md5collision/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/evilize-md5coll`

Backlinks: [[cracker]] · [[evilize]] · [[BlackArch Tools]]

