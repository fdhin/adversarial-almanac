---
tool: "tcgetkey"
build-system: "make"
upstream: "http://packetstormsecurity.com/files/119146/tcgetkey-0.1.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/119146/tcgetkey-0.1.html"
license: "unknown"
blackarch-pkgver: "0.1"
---

# Installing tcgetkey

> A set of tools that deal with acquiring physical memory dumps via FireWire and then scan the memory dump to locate TrueCrypt keys and finally decrypt the encrypted TrueCrypt container using the keys.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tcgetkey) (version `0.1`, license unknown).

## Arch / BlackArch

```bash
sudo pacman -S tcgetkey    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/119146/tcgetkey.1.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libforensic1394`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[tcgetkey]] · [[BlackArch Tools]]

