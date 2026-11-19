---
tool: "admid-pack"
build-system: "make"
upstream: "http://packetstormsecurity.com/files/10080/ADMid-pkg.tgz.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/groups/ADM/ADM-DNS-SPOOF/ADMid-pkg.tgz"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing admid-pack

> ADM DNS spoofing tools - Uses a variety of active and passive methods to spoof DNS packets. Very powerful.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/admid-pack) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S admid-pack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
curl -fLO https://dl.packetstormsecurity.net/groups/ADM/ADM-DNS-SPOOF/ADMid-pkg.tgz
tar xf ADMid-pkg.tgz
cd ADMid-pkg
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$_pkgname/src"

  make CC="gcc $CFLAGS"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[admid-pack]] · [[BlackArch Tools]]

