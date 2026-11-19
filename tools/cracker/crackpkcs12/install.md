---
tool: "crackpkcs12"
build-system: "make"
upstream: "https://github.com/crackpkcs12/crackpkcs12"
recipe: "blackarch"
aur-package: "crackpkcs12"
source: "https://github.com/crackpkcs12/crackpkcs12"
license: "GPL3"
blackarch-pkgver: "0.2.10.11.c7dedb1"
---

# Installing crackpkcs12

> A multithreaded program to crack PKCS#12 files (p12 and pfx extensions).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crackpkcs12) (version `0.2.10.11.c7dedb1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S crackpkcs12    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/crackpkcs12/crackpkcs12) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[crackpkcs12]] · [[BlackArch Tools]]

