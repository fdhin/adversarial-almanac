---
tool: "blacknurse"
build-system: "make"
upstream: "https://github.com/jedisct1/blacknurse"
recipe: "blackarch"
source: "https://github.com/jedisct1/blacknurse"
license: "BSD2"
blackarch-pkgver: "12.934b6a2"
---

# Installing blacknurse

> A low bandwidth ICMP attack that is capable of doing denial of service to well known firewalls.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/blacknurse) (version `12.934b6a2`, license BSD2).

## Arch / BlackArch

```bash
sudo pacman -S blacknurse    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/jedisct1/blacknurse) (the recipe uses a versioned archive).

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

Backlinks: [[dos]] · [[blacknurse]] · [[BlackArch Tools]]

