---
tool: "narthex"
build-system: "make"
upstream: "https://github.com/MichaelDim02/Narthex"
recipe: "blackarch"
source: "https://github.com/MichaelDim02/Narthex"
license: "GPL3"
blackarch-pkgver: "v1.2.r7.g8b78746"
provides: ["nrev", "nwiz"]
---

# Installing narthex

> Modular personalized dictionary generator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/narthex) (version `v1.2.r7.g8b78746`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S narthex    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/MichaelDim02/Narthex.git
cd Narthex
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/nrev`, `/usr/bin/nwiz`
- Data: `/usr/share/man`

Backlinks: [[misc]] · [[narthex]] · [[BlackArch Tools]]

