---
tool: "hidattack"
build-system: "make"
upstream: "http://mulliner.org/bluetooth/hidattack.php"
recipe: "blackarch"
source: "http://mulliner.org/bluetooth/hidattack.php"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing hidattack

> HID Attack (attacking HID host implementations).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hidattack) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S hidattack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://mulliner.org/bluetooth/hidattack.php) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`
- **System libraries** (Arch name — translate for your distro):
  - `bluez`
  - `openobex`

## Exact build steps from the recipe

```bash
cd "${pkgname}${pkgver//./}"

  make CFLAGS="$CFLAGS"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[hidattack]] · [[BlackArch Tools]]

