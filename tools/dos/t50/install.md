---
tool: "t50"
build-system: "make"
upstream: "https://gitlab.com/fredericopissarra/t50/-/releases"
recipe: "blackarch"
aur-package: "t50"
source: "https://gitlab.com/fredericopissarra/t50/-/releases"
license: "GPL2"
blackarch-pkgver: "5.8.7c"
kali-package: "t50"
kali-version: "5.8.7c-2"
---

# Installing t50

> Experimental Multi-protocol Packet Injector Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/t50) (version `5.8.7c`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S t50    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install t50    # Kali package version 5.8.7c-2
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://gitlab.com/fredericopissarra/t50/-/releases) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `linux-headers`

## Exact build steps from the recipe

```bash
cd "$pkgname-v$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[dos]] · [[t50]] · [[BlackArch Tools]]

