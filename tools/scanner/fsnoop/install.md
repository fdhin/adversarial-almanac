---
tool: "fsnoop"
build-system: "make"
upstream: "http://vladz.devzero.fr/fsnoop.php"
recipe: "blackarch"
source: "http://vladz.devzero.fr/fsnoop.php"
license: "Beer-Ware"
blackarch-pkgver: "3.4"
---

# Installing fsnoop

> A tool to monitor file operations on GNU/Linux systems by using the Inotify mechanism. Its primary purpose is to help detecting file race condition vulnerabilities and since version 3, to exploit them with loadable DSO modules (also called "payload modules" or "paymods").

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fsnoop) (version `3.4`, license Beer-Ware).

## Arch / BlackArch

```bash
sudo pacman -S fsnoop    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://vladz.devzero.fr/fsnoop.php) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[fsnoop]] · [[BlackArch Tools]]

