---
tool: "gcrypt"
build-system: "other"
upstream: "https://gitlab.com/GasparVardanyan/gcrypt"
recipe: "blackarch"
source: "https://gitlab.com/GasparVardanyan/gcrypt"
license: "MIT"
blackarch-pkgver: "32.d3f9f06"
---

# Installing gcrypt

> Simple, secure and performance file encryption tool written in C

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gcrypt) (version `32.d3f9f06`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gcrypt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://gitlab.com/GasparVardanyan/gcrypt
cd gcrypt
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libbsd`

## Exact build steps from the recipe

```bash
cd $pkgname

  gcc -o $pkgname *.c -std=c18 -pedantic -pedantic-errors -Wall -O \
    -fstack-protector-all -fPIE -fPIC \
    -D_FORTIFY_SOURCE=2 -Wl,-z,now -Wl,-z,-relro -lbsd
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[gcrypt]] · [[BlackArch Tools]]

