---
tool: "nfsshell"
build-system: "make"
upstream: "http://www.paramecium.org/~leendert/"
recipe: "blackarch"
aur-package: "nfsshell"
source: "http://www.paramecium.org/~leendert/"
license: "custom:unknown"
blackarch-pkgver: "19980519"
---

# Installing nfsshell

> Userland NFS command tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nfsshell) (version `19980519`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S nfsshell    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.paramecium.org/~leendert/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `libtirpc`
  - `libtirpc-compat`
  - `readline`

## Exact build steps from the recipe

```bash
cd nfs

  patch -Np1 -i "$srcdir/gcc.patch"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[nfsshell]] · [[BlackArch Tools]]

