---
tool: "airflood"
build-system: "make"
upstream: "http://packetstormsecurity.com/files/51127/airflood-0.1.tar.gz.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/51127/airflood-0.1.tar.gz.html"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing airflood

> A modification of aireplay that allows for a DoS of the AP. This program fills the table of clients of the AP with random MACs doing impossible new connections. [Tool in Spanish]

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/airflood) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S airflood    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/51127/airflood.1.tar.gz.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `lib32-glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[airflood]] · [[BlackArch Tools]]

