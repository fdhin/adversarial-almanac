---
tool: "hexinject"
build-system: "make"
upstream: "http://hexinject.sourceforge.net"
recipe: "blackarch"
aur-package: "hexinject"
source: "http://hexinject.sourceforge.net"
license: "BSD"
blackarch-pkgver: "1.6"
provides: ["hex2raw", "prettypacket"]
kali-package: "hexinject"
kali-version: "1.6-0kali3+b1"
---

# Installing hexinject

> A very versatile packet injector and sniffer that provides a command-line framework for raw network access.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hexinject) (version `1.6`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S hexinject    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install hexinject    # Kali package version 1.6-0kali3+b1
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://hexinject.sourceforge.net) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/hex2raw`, `/usr/bin/prettypacket`

Backlinks: [[sniffer]] · [[hexinject]] · [[BlackArch Tools]]

