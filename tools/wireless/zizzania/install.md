---
tool: "zizzania"
build-system: "make"
upstream: "https://github.com/cyrus-and/zizzania"
recipe: "blackarch"
source: "https://github.com/cyrus-and/zizzania"
license: "custom:unknown"
blackarch-pkgver: "124.8f2062f"
---

# Installing zizzania

> Automated DeAuth attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zizzania) (version `124.8f2062f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S zizzania    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/cyrus-and/zizzania) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `scons`
  - `uthash`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[zizzania]] · [[BlackArch Tools]]

