---
tool: "tyton"
build-system: "make"
upstream: "https://github.com/nbulischeck/tyton"
recipe: "blackarch"
source: "https://github.com/nbulischeck/tyton"
license: "GPL3"
blackarch-pkgver: "81.f740076"
---

# Installing tyton

> Kernel-Mode Rootkit Hunter.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tyton) (version `81.f740076`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S tyton    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/nbulischeck/tyton) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `dkms`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel
  - `libnotify`
  - `libsystemd`
  - `linux-headers`

## Exact build steps from the recipe

```bash
cd $pkgname

  make DESTDIR="$pkgdir"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[tyton]] · [[BlackArch Tools]]

