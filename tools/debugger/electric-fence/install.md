---
tool: "electric-fence"
build-system: "make"
upstream: "https://packages.debian.org/sid/electric-fence"
recipe: "blackarch"
source: "https://packages.debian.org/sid/electric-fence"
license: "GPL2"
blackarch-pkgver: "2.2.6"
---

# Installing electric-fence

> A malloc(3) debugger that uses virtual memory hardware to detect illegal memory accesses.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/electric-fence) (version `2.2.6`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S electric-fence    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://packages.debian.org/sid/electric-fence) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `glibc`

## Exact build steps from the recipe

```bash
make -C $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[debugger]] · [[electric-fence]] · [[BlackArch Tools]]

