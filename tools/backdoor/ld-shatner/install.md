---
tool: "ld-shatner"
build-system: "make"
upstream: "https://github.com/sduverger/ld-shatner"
recipe: "blackarch"
source: "https://github.com/sduverger/ld-shatner"
license: "custom:unknown"
blackarch-pkgver: "4.5c215c4"
provides: ["interpatch"]
---

# Installing ld-shatner

> ld-linux code injector.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ld-shatner) (version `4.5c215c4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ld-shatner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/sduverger/ld-shatner) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `gcc-libs`

## Exact build steps from the recipe

```bash
cd $pkgname

  _opt=""

  gcc -fpie -c obj.c -o obj.o
  ld -pie obj.o -o obj.elf -e func

  _opt="-m32"

  gcc hooked.s "$pkgname.c" $_opt -o $pkgname

  gcc interpatch.c -o interpatch

  #make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/interpatch`

Backlinks: [[backdoor]] · [[ld-shatner]] · [[BlackArch Tools]]

