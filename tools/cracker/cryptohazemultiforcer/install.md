---
tool: "cryptohazemultiforcer"
build-system: "scripts"
upstream: "http://www.cryptohaze.com/multiforcer.php"
recipe: "blackarch"
source: "http://www.cryptohaze.com/multiforcer.php"
license: "custom"
blackarch-pkgver: "1.31a"
---

# Installing cryptohazemultiforcer

> High performance multihash brute forcer with CUDA support.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cryptohazemultiforcer) (version `1.31a`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S cryptohazemultiforcer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.cryptohaze.com/multiforcer.php) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[cryptohazemultiforcer]] · [[BlackArch Tools]]

