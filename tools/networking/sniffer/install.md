---
tool: "sniffer"
build-system: "make"
upstream: "https://github.com/julioreynaga/sniffer"
recipe: "blackarch"
aur-package: "sniffer"
source: "https://github.com/julioreynaga/sniffer"
license: "custom:unknown"
blackarch-pkgver: "4.688854e"
provides: ["cookie-parser", "email-parser", "packet-parser", "tcp-parser"]
---

# Installing sniffer

> Packet Trace Parser for TCP, SMTP Emails, and HTTP Cookies.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sniffer) (version `4.688854e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sniffer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/julioreynaga/sniffer) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `clang`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/cookie-parser`, `/usr/bin/email-parser`, `/usr/bin/packet-parser`, `/usr/bin/tcp-parser`

Backlinks: [[networking]] · [[sniffer]] · [[BlackArch Tools]]

