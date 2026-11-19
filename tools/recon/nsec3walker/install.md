---
tool: "nsec3walker"
build-system: "make"
upstream: "https://dnscurve.org/nsec3walker.html"
recipe: "blackarch"
aur-package: "nsec3walker"
source: "https://dnscurve.org/nsec3walker.html"
license: "custom"
blackarch-pkgver: "20101223"
---

# Installing nsec3walker

> Enumerate domain names using DNSSEC.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nsec3walker) (version `20101223`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S nsec3walker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://dnscurve.org/nsec3walker.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[nsec3walker]] · [[BlackArch Tools]]

