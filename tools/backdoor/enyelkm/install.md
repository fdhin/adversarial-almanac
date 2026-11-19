---
tool: "enyelkm"
build-system: "scripts"
upstream: "http://www.enye-sec.org/programas.html"
recipe: "blackarch"
source: "http://www.enye-sec.org/programas.html"
license: "GPL"
blackarch-pkgver: "1.2"
---

# Installing enyelkm

> Rootkit for Linux x86 kernels v2.6.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/enyelkm) (version `1.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S enyelkm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.enye-sec.org/programas.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[enyelkm]] · [[BlackArch Tools]]

