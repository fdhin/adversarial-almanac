---
tool: "sslnuke"
build-system: "make"
upstream: "https://github.com/jtripper/sslnuke"
recipe: "blackarch"
source: "https://github.com/jtripper/sslnuke"
license: "GPL2"
blackarch-pkgver: "5.c5faeaa"
---

# Installing sslnuke

> Transparent proxy that decrypts SSL traffic and prints out IRC messages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sslnuke) (version `5.c5faeaa`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S sslnuke    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/jtripper/sslnuke) (the recipe uses a versioned archive).

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

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[sslnuke]] · [[BlackArch Tools]]

