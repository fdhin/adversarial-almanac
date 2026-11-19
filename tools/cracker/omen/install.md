---
tool: "omen"
build-system: "make"
upstream: "https://github.com/RUB-SysSec/OMEN"
recipe: "blackarch"
source: "https://github.com/RUB-SysSec/OMEN"
license: "MIT"
blackarch-pkgver: "19.10aa99e"
provides: ["omen-alphabetcreator", "omen-createng", "omen-enumng", "omen-evalpw"]
---

# Installing omen

> Ordered Markov ENumerator - Password Guesser.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/omen) (version `19.10aa99e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S omen    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/RUB-SysSec/OMEN.git
cd OMEN
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

- Commands: `/usr/bin/omen-alphabetcreator`, `/usr/bin/omen-createng`, `/usr/bin/omen-enumng`, `/usr/bin/omen-evalpw`

Backlinks: [[cracker]] · [[omen]] · [[BlackArch Tools]]

