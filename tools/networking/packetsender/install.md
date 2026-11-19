---
tool: "packetsender"
build-system: "make"
upstream: "https://github.com/dannagle/PacketSender"
recipe: "blackarch"
aur-package: "packetsender"
source: "https://github.com/dannagle/PacketSender"
license: "GPL2"
blackarch-pkgver: "1340.ef0f0c7"
---

# Installing packetsender

> An open source utility to allow sending and receiving TCP and UDP packets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/packetsender) (version `1340.ef0f0c7`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S packetsender    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/dannagle/PacketSender.git
cd PacketSender
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glib2`
  - `libcap`
  - `libgcrypt`
  - `libpng`
  - `libxcb`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `qt5-base`

## Exact build steps from the recipe

```bash
cd "$pkgname/src"

  qmake PacketSender.pro

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[packetsender]] · [[BlackArch Tools]]

