---
tool: "rtl-wmbus"
build-system: "make"
upstream: "https://github.com/xaelsouth/rtl-wmbus"
recipe: "blackarch"
source: "https://github.com/xaelsouth/rtl-wmbus"
license: "custom:unknown"
blackarch-pkgver: "1.1.0.r0.g34684e6"
---

# Installing rtl-wmbus

> Software defined receiver for wireless M-Bus with RTL-SDR.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rtl-wmbus) (version `1.1.0.r0.g34684e6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rtl-wmbus    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/xaelsouth/rtl-wmbus) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `rtl-sdr`

## Exact build steps from the recipe

```bash
cd $pkgname

    make release 'CFLAGS=-Iinclude -std=gnu99'
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[rtl-wmbus]] · [[BlackArch Tools]]

