---
tool: "uw-loveimap"
build-system: "make"
upstream: "http://uberwall.org/bin/download/45/UWloveimap.tgz"
recipe: "blackarch"
source: "http://uberwall.org/bin/download/download/45/UWloveimap.tgz"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing uw-loveimap

> Multi threaded imap bounce scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uw-loveimap) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S uw-loveimap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
curl -fLO http://uberwall.org/bin/download/download/45/UWloveimap.tgz
tar xf UWloveimap.tgz
cd UWloveimap
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd UWloveimap

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[uw-loveimap]] · [[BlackArch Tools]]

