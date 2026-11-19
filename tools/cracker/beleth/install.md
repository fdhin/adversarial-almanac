---
tool: "beleth"
build-system: "make"
upstream: "https://github.com/chokepoint/Beleth"
recipe: "blackarch"
source: "https://github.com/chokepoint/Beleth"
license: "custom"
blackarch-pkgver: "36.0963699"
---

# Installing beleth

> A Multi-threaded Dictionary based SSH cracker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/beleth) (version `36.0963699`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S beleth    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/chokepoint/Beleth.git
cd Beleth
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libssh2`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[beleth]] · [[BlackArch Tools]]

