---
tool: "voiphopper"
build-system: "autotools"
upstream: "http://voiphopper.sourceforge.net/"
recipe: "blackarch"
source: "http://voiphopper.sourceforge.net/"
license: "GPL3"
blackarch-pkgver: "2.04"
kali-package: "voiphopper"
kali-version: "2.04-1kali7"
---

# Installing voiphopper

> A security validation tool that tests to see if a PC can mimic the behavior of an IP Phone. It rapidly automates a VLAN Hop into the Voice VLAN.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/voiphopper) (version `2.04`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S voiphopper    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install voiphopper    # Kali package version 2.04-1kali7
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://voiphopper.sourceforge.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `gcc`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --mandir=/usr/share/man --libexecdir=/usr/lib

  sed -i "s%<linux/if_ether.h>%<netinet/if_ether.h>%" src/dhcpclient.h
  sed -i "s%<linux/if_tr.h>%<netinet/if_tr.h>%" src/dhcpclient.h

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[voiphopper]] · [[BlackArch Tools]]

