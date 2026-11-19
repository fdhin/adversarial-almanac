---
tool: "mdcrack"
build-system: "make"
upstream: "http://c3rb3r.openwall.net/mdcrack/"
recipe: "blackarch"
aur-package: "mdcrack"
source: "http://c3rb3r.openwall.net/mdcrack/"
license: "custom:unknown"
blackarch-pkgver: "1.2"
---

# Installing mdcrack

> MD4/MD5/NTLM1 hash cracker

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mdcrack) (version `1.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S mdcrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://c3rb3r.openwall.net/mdcrack/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make little
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[mdcrack]] · [[BlackArch Tools]]

