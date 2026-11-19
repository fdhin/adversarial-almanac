---
tool: "mptcp"
build-system: "make"
upstream: "http://packetstormsecurity.com/files/119132/Mptcp-Packet-Manipulator-1.9.0.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/119132/Mptcp-Packet-Manipulator-1.9.0.html"
license: "GPL3"
blackarch-pkgver: "1.9.0"
---

# Installing mptcp

> A tool for manipulation of raw packets that allows a large number of options.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mptcp) (version `1.9.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S mptcp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/119132/Mptcp-Packet-Manipulator.9.0.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "MpTcp-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man8`

Backlinks: [[networking]] · [[mptcp]] · [[BlackArch Tools]]

