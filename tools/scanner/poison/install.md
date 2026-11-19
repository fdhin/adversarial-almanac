---
tool: "poison"
build-system: "make"
upstream: "http://nologin.org/main.pl?action=codeList&"
recipe: "blackarch"
source: "http://nologin.org/main.pl?action=codeList&"
license: "GPL"
blackarch-pkgver: "1.5.41"
---

# Installing poison

> A fast, asynchronous syn and udp scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/poison) (version `1.5.41`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S poison    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://nologin.org/main.pl?action=codeList&) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[poison]] · [[BlackArch Tools]]

