---
tool: "sipffer"
build-system: "make"
upstream: "https://github.com/xenomuta/SIPffer"
recipe: "blackarch"
source: "https://github.com/xenomuta/SIPffer"
license: "GPL3"
blackarch-pkgver: "29.efc3ff1"
---

# Installing sipffer

> SIP protocol command line sniffer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sipffer) (version `29.efc3ff1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sipffer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/xenomuta/SIPffer.git
cd SIPffer
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `pcre`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[sipffer]] · [[BlackArch Tools]]

