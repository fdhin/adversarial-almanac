---
tool: "nield"
build-system: "autotools"
upstream: "http://nield.sourceforge.net/"
recipe: "blackarch"
source: "http://nield.sourceforge.net/"
license: "GPL2"
blackarch-pkgver: "41.cd96f76"
---

# Installing nield

> A tool to receive notifications from kernel through netlink socket, and generate logs related to interfaces, neighbor cache(ARP,NDP), IP address(IPv4,IPv6), routing, FIB rules, traffic control.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nield) (version `41.cd96f76`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S nield    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://nield.sourceforge.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  if [[ "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr --sbindir=/usr/bin
  else
    ./configure --build=arm --prefix=/usr --sbindir=/usr/bin
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[nield]] · [[BlackArch Tools]]

