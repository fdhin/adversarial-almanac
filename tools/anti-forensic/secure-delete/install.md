---
tool: "secure-delete"
build-system: "make"
upstream: "http://www.thc.org/"
recipe: "blackarch"
source: "http://www.thc.org/"
license: "GPL"
blackarch-pkgver: "1.b63d814"
provides: ["smem", "smem-secure-delete"]
---

# Installing secure-delete

> Secure file, disk, swap, memory erasure utilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/secure-delete) (version `1.b63d814`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S secure-delete    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.thc.org/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/smem`, `/usr/bin/smem-secure-delete`
- Data: `/usr/share/man`

Backlinks: [[anti-forensic]] · [[secure-delete]] · [[BlackArch Tools]]

