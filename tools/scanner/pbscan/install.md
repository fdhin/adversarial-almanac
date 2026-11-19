---
tool: "pbscan"
build-system: "make"
upstream: "https://github.com/gvb84/pbscan"
recipe: "blackarch"
source: "https://github.com/gvb84/pbscan"
license: "BSD"
blackarch-pkgver: "10.566c3d7"
---

# Installing pbscan

> Faster and more efficient stateless SYN scanner and banner grabber due to userland TCP/IP stack usage.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pbscan) (version `10.566c3d7`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S pbscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/gvb84/pbscan) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  unset CFLAGS

  make -j1
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[pbscan]] · [[BlackArch Tools]]

