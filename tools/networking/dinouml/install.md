---
tool: "dinouml"
build-system: "make"
upstream: "http://kernel.embedromix.ro/us/"
recipe: "blackarch"
source: "http://kernel.embedromix.ro/us/"
license: "GPL"
blackarch-pkgver: "0.9.5"
---

# Installing dinouml

> A network simulation tool, based on UML (User Mode Linux) that can simulate big Linux networks on a single PC

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dinouml) (version `0.9.5`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dinouml    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://kernel.embedromix.ro/us/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "DinoUML-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[networking]] · [[dinouml]] · [[BlackArch Tools]]

