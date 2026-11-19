---
tool: "vstt"
build-system: "make"
upstream: "http://www.wendzel.de/dr.org/files/Projects/vstt/"
recipe: "blackarch"
source: "http://www.wendzel.de/dr.org/files/Projects/vstt/"
license: "BSD"
blackarch-pkgver: "0.5.3"
---

# Installing vstt

> VSTT is a multi-protocol tunneling tool. It accepts input by TCP stream sockets and FIFOs, and can send data via TCP, POP3, and ICMP tunneling.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vstt) (version `0.5.3`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S vstt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.wendzel.de/dr.org/files/Projects/vstt/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[vstt]] · [[BlackArch Tools]]

